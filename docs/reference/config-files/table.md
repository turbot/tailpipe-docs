---
title: table
---


# table

The `table` block allows you to define custom Tailpipe tables.  Custom tables are a means to collect data from arbitrary log files and other sources.  This is useful if you need to analyze logs that have custom or proprietary formats or collect logs that do not have plugin support.


```hcl
table "openstack_syslog" {
  map_fields = ['*']
  format     = format.regex.openstack_syslog
  null_if    = "-"

  column "tp_timestamp"{
    source = "timestamp"
  }

  column "tp_index" {
    source = "tenant_id"
  }
}
```


Table blocks have a single label, which is the name of the table. They define the schema and processing rules for your data, specifying how to parse and transform your source data into columns.

The format of the source data is defined by the `format` property, which must reference either a format block defined in the config or a format preset defined by a plugin.


## Arguments

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `column`     | block    | Optional  | One or more [column blocks](#column-blocks) to define columns for your table and map/transform data from the [source](/docs/collect/configure#sources).
| `format`     | [format](/docs/reference/config-files/format) reference | Optional  | The default format of the source data. This must refer to either a `format` block or a format preset defined by a plugin. `format` is optional on the table definition.  If omitted, it must be specified within the source block.
| `map_fields` | List     | Optional  | A list of fields from the source to include as columns in the table.  This list can contain wildcards, and the default is all fields (`["*"]`).
| `null_if`    | String   | Optional  | A value that should be translated to `null` when it occurs in the source data. For example, if the value is `-`, then any column in the source data which has a value of `-` will be translated to `null` in the resulting parquet file.


## Column blocks
Column blocks allow you to define columns for your table and map and transform data from the [source](/docs/collect/configure#sources). 

```hcl
table  "my_table" {

  column "user_id" {
    source      = "uid"
    description = "User ID"
    required    = true
    null_if     = "-"
  }

  column "tp_timestamp" {
    type        = "timestamp"
    description = "Timestamp of the event"
    required    = true
    transform   = "strptime(event_time, '%d/%m/%y-%H|%M|%S@%Z')"
  }
}
```

Note that you can use `map_fields` to add fields from the source to the table without transforming them.  Use `column` blocks when you need to transform or customize the columns, for example:

- To specify a column to include in this table which does not exist in the source data.  In this case, either the `source` property should specify which field in the source field to use, or the `transform` property should be used to specify a DuckDB expression or function to use to produce a value for the column.
- To provide the source data mapping for the [Tailpipe common (`tp_`) fields](/docs/reference/config-files/table#common-columns). ***You must provide a mapping for the `tp_timestamp` field***, but the rest are all optional (but encouraged).
- To forcibly specify the data type of a column that exists in the source data. By default, DuckDB type inference is used to determine the type of a column based on the source data type.  This automatic typing is usually sufficient, but there are cases where the automatic type is incorrect or insufficient. For example, in cases where a field usually looks like a UUID but is not always a UUID, it may be useful to specify a string type. Otherwise, DuckDB may infer the type as a UUID, and any non-UUID values will result in an error when collecting data.

### Column Arguments

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `type`       | String   | Optional  | The [data type]() of the column. If the column is required, the type is optional; DuckDB will infer the type from the source data.  If the column is optional, then the type must be specified.
| `description`| String   | Optional  | A description of the column. This is used to generate documentation for the table.
| `required`   | Boolean  | Optional  | If set to `true`, then the column is required, and a validation error will be raised if the column is not present in the source data.
| `source`     | String   | Optional  | The field in the source data to use for this column.
| `transform`     | String   | Optional  | A DuckDB transform function to apply to the column. This should be expressed as a [SQL function](https://duckdb.org/docs/stable/sql/functions/overview.html). If a `transform` is provided, no `source` should be provided.


### Column Data Types

Tailpipe supports most of the [DuckDB general-purpose data types](https://duckdb.org/docs/stable/sql/data_types/overview.html#general-purpose-data-types):

| Type       | Description
|------------|---------------------------------
| `boolean`  | Logical Boolean (true / false)
| `tinyint`  | Signed one-byte integer
| `smallint` | Signed two-byte integer
| `integer`  | Signed four-byte integer
| `bigint`   | Signed eight-byte integer
| `utinyint` | Unsigned one-byte integer
| `usmallint`| Unsigned two-byte integer
| `uinteger` | Unsigned four-byte integer
| `ubigint`  | Unsigned eight-byte integer
| `float`    | Single precision floating-point number (4 bytes)
| `double`   | Double precision floating-point number (8 bytes)
| `varchar`  | Variable-length character string
| `blob`     | Variable-length binary data
| `date`     | Calendar date (year, month day)
| `timestamp`| Combination of time and date
| `time`     | Time of day (no time zone)
| `interval` | Date / time delta
| `decimal`  | Fixed-precision number with the given width (precision) and scale, defaults to prec = 18 and scale = 3
| `uuid`     | UUID data type
| `json`     | JSON object (via the json extension)


## Common Columns

Tailpipe tables include a set of common columns.  These mappings enable queries that correlate values across different logs. If you have collected both Cloudtrail and ALB logs, for example, you could query for `tp_ips` to find IP addresses in the `aws_cloudtrail_log` and `aws_alb_access_log` tables using the same syntax.

When creating a custom table, `tp_timestamp` is the only required column; ***you must define a `tp_timestamp` column***.  This is because Tailpipe uses the timestamp to [organize the data files](/docs/collect/configure#hive-partitioning).  The `tp_index` is also used in the hive partitioning scheme.  By default, `tp_index` is set to `"default"`, but you can configure it in your partition config to specify a different value or expression for the partition index.

Some of the common columns (`tp_date`,`tp_id`,`tp_ingest_timestamp`,`tp_partition`,`tp_table`) are automatically set by the plugins - You do not need to create them.  Others are optional (but encouraged).  If you do not set an optional common column, all values will be `null`.


| Field Name           | Required | Type        | Description
|----------------------|----------|-------------|---------------------------------------------------------------------------------
| tp_akas              | optional | `varchar[]` | A list of associated globally unique identifier strings (also known as).
| tp_date              | auto     | `date`      | The original date when the event or log entry was generated in YYYY-MM-DD format.
| tp_destination_ip    | optional | `varchar`   | The IP address of the destination.
| tp_domains           | optional | `varchar[]` | A list of associated domain names.
| tp_emails            | optional | `varchar[]` | A list of associated email addresses.
| tp_id                | auto     | `varchar`   | A unique identifier for the row.
| tp_index             | auto     | `varchar`   | The name of the optional index used to partition the data.
| tp_ingest_timestamp  | auto     | `timestamp` | The timestamp in UTC when the row was ingested into the system.
| tp_ips               | optional | `varchar[]` | A list of associated IP addresses.
| tp_partition         | auto     | `varchar`   | The name of the partition as defined in the Tailpipe configuration file.
| tp_source_ip         | auto     | `varchar`   | The IP address of the source.
| tp_source_location   | auto     | `varchar`   | The geographic or network location of the source, such as a region.
| tp_source_name       | auto     | `varchar`   | The name or identifier of the source generating the row, such as a service name.
| tp_source_type       | optional | `varchar`   | The name of the source that collected the row.
| tp_table             | auto     | `varchar`   | The name of the table.
| tp_tags              | optional | `varchar[]` | A list of associated tags or labels.
| tp_timestamp         | required | `timestamp` | The original timestamp in UTC when the event or log entry was generated.
| tp_usernames         | optional | `varchar[]` | A list of associated usernames or identities.


## Examples

To create a custom table, define a `table` and then create one or more partitions for the table.

```hcl
table "openstack_syslog" {
  format  = format.regex.openstack_syslog
  null_if = "-"

  column "tp_timestamp"{
    source = "timestamp"
  }

  column "tp_index" {
    source = "tenant_id"
  }
  column "tenant_id" {
    source = "tenant_id"
    type = "uuid"
  }
}

format "regex" "openstack_syslog"{
  layout = `^(?P<log_file>nova-[\w-]+\.log(?:\.\d+)?\.[\d-]+_[\d:]+)\s+(?P<timestamp>[\d-]+\s+[\d:.]+)\s+(?P<pid>\d+)\s+(?P<log_level>\w+)\s+(?P<component>[\w._-]+)(?:\s+(?:\[(?:req-(?P<request_id>[^\s]+)\s+(?P<user_id>[^\s]+)\s+(?P<tenant_id>[^\s]+)(?:\s+[^\]]+)?|-)]\s+)?(?:(?P<client_ip>\d+\.\d+\.\d+\.\d+)\s+"(?P<http_method>GET|POST|PUT|DELETE)\s+(?P<http_path>[^\s]+)\s+HTTP\/[\d.]+"\s+status:\s+(?P<http_status>\d+)\s+len:\s+(?P<resp_size>\d+)\s+time:\s+(?P<resp_time>[\d.]+)|(?:\[instance:\s+(?P<instance_id>[^\]]+)\])?\s*(?P<message>.*))?)?$`
}

partition "openstack_syslog" "midterms" {
  source "file"  {
    paths       = ["/Downloads/OpenStack"]
    file_layout = ".log$"
  }
}

```


