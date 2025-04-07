---
title: table
---


# table

The `table` block allows you to define custom Tailpipe tables.  Custom tables are a means to collect data from arbitrary log files and other sources.  This is useful if you need to analyze logs that have custom or proprietary formats, or to collect logs that do not have plugin support.


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
| `format`     | [format](reference/format) reference | Required  |  The default format of the source data. This must refer to either a `format` block defined in the config, or a format preset defined by a plugin.
| `column`     | block    | Optional  | One or more [column blocks](#column-blocks) to define columns for your table and map/transform data from the [source](manage/partition#source).
| `map_fields` | List     | Optional  | A list of fields from the source to include as columns in the table.  This list can contain wildcards, and the default is all fields (`["*"]`).
| `null_if`    | String   | Optional  | The value which is translated to null when it occurs in the source data. For example, if the null value is "-", then any column in the source data which has a value of "-" will be translated to NULL in the resulting parquet file.



## Column blocks
Column block allow you to define columns for your table and map and transform data from the [source](manage/partition#source). 

```hcl
table  "my_table" {

  column "user_id" {
    type        = "varchar"
    description = "User id"
    required    = true
    null_value  = "-"
    transform   =  "upper(user_id)"
  }

  column "tp_timestamp" {
    type        = "timestamp"
    source      = "event_time"
    description = "Timestamp of the event"
    required    = true
    time_format = "%d/%m/%y-%H|%M|%S@%Z"
  }
}
```

Note that you can use `map_fields` to add fields from the source to the table without transforming them.  Use `column` blocks when you need to transform or customize the columns, for example:

- To specify a column to include in this table which does not exist in the source data. 
For this usage, either the `source` property is used specify which field in the source field to use, or the `transform` property should be used to specify a DuckDB expression or function to use to produce a value for the column.
- To provide the source data mapping for the [Tailpipe common (`tp_`) fields](#common-fields). ***You must provide a mapping for the `tp_timestamp` field*** but the rest are all optional (but encouraged).
- To forcibly specify the data type of a column which exists in the source data. By default, DuckDB type inference is used to determine the type of a column based on the source data type.  tis automatic typing is usually sufficient, but there are cases where the automatic type is incorrect or insufficient. For example, in cases where a field usually looks like a GUID but is not always a GUID, it may be useful to 
specify a string type - otherwise if DuckDB infers the type as a GUID, then any non-GUID values will result in an error when collecting data.

### Column Arguments

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `type`       | String   | Optional  | The [data type]() of the column. If the column is required, the type is optional as DuckDB will infer the type from the source data.
  If the column is optional, then the type must be specified.
| `description`| String   | Optional  | a description of the column. This is used to generate documentation for the table.
| `required`   | Boolean  | Optional  | if set to `true`, then the column is required and a validation error will be raised if the column is not present in the source data.
| `source`     | String   | Optional  | The field in the source data to use for this column.
| `time_format`| String   | Optional  | A string, in [`strptime` format](https://duckdb.org/docs/stable/sql/functions/dateformat.html#strptime-examples) that allows the source field to be mapped to a timestamp.
| `transform`     | String   | Optional  | A duck DB transform function to apply to the column. This should be expressed as a [SQL function](https://duckdb.org/docs/stable/sql/functions/overview.html). If a `transform` is provided, no `source` should be provided.

 


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



## Examples

To create a custom table, define a `table` and then create one or more partitions for the table.

```hcl
table "openstack_syslog" {
  format     = format.regex.openstack_syslog
  null_value = "-"

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


