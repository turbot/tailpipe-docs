---
title: format
---

# format

The `format` block enables you to define source formats for custom tables.  Formats describe the layout of the source data so that it can be collected into a table.

Format blocks have 2 labels:
- the [format type](#format-types)
- the name of the table


```hcl
format "regex" "openstack_syslog"{
  layout = `^(?P<log_file>nova-[\w-]+\.log(?:\.\d+)?\.[\d-]+_[\d:]+)\s+(?P<timestamp>[\d-]+\s+[\d:.]+)\s+(?P<pid>\d+)\s+(?P<log_level>\w+)\s+(?P<component>[\w._-]+)(?:\s+(?:\[(?:req-(?P<request_id>[^\s]+)\s+(?P<user_id>[^\s]+)\s+(?P<tenant_id>[^\s]+)(?:\s+[^\]]+)?|-)]\s+)?(?:(?P<client_ip>\d+\.\d+\.\d+\.\d+)\s+"(?P<http_method>GET|POST|PUT|DELETE)\s+(?P<http_path>[^\s]+)\s+HTTP\/[\d.]+"\s+status:\s+(?P<http_status>\d+)\s+len:\s+(?P<resp_size>\d+)\s+time:\s+(?P<resp_time>[\d.]+)|(?:\[instance:\s+(?P<instance_id>[^\]]+)\])?\s*(?P<message>.*))?)?$`
}
```

## Format Types

The format type defines the parsing mechanism which should be used. The properties of the `format` are specific to the format type.

Formats types are implemented by plugins. A number of formats types are provided by the `core` plugin, which is included in every Tailpipe installation.

Instances of a format type can be defined in a `format` block. Also, plugins may export format `presets` which may be referenced by name.   These may be discovered using the introspection commands: `tailpipe plugin show <plugin name>` or `tailpipe format list`


### Core Plugin Formats

#### Grok Format
The `grok` format is used for parsing log lines using [Grok patterns](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html#_grok_basics), which are a way to parse log lines into structured data.

```hcl
format "grok" "custom_log" {
  layout = "%{TIMESTAMP_ISO8601:time_local} - %{NUMBER:event_id} - %{WORD:user} - [%{DATA:location}] \"%{DATA:message}\" %{WORD:severity}"
  patterns = {
    "REGION" = "[a-zA-Z0-9\\-]+"
  }
}
```

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `layout`     | String   | Required  | The Grok pattern that defines how to parse the log line
| `patterns`   | Map      | Optional  | A map of custom Grok patterns that can be referenced in the layout
| `description`| String   | Optional  | A description of the format


#### Regex Format
The `regex` format is used for parsing log lines using regular expressions with named capture groups.

```hcl
format "regex" "custom_log" {
  layout = `^(?P<timestamp>[\d-]+\s+[\d:.]+)\s+(?P<pid>\d+)\s+(?P<log_level>\w+)\s+(?P<component>[\w._-]+)`
}
```

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `layout`     | String   | Required  | The regular expression pattern with named capture groups
| `description`| String   | Optional  | A description of the format

#### Delimited Format
The `delimited` format is used for parsing CSV, TSV, and other delimited file formats. The properties are passed directly to DuckDB which implements the delimited data parsing.

```hcl
format "delimited" "custom_csv" {
  delimiter = ","
  header    = true
  null_str  = "-"
}
```

| Argument            | Type     | Optional? | Description
|---------------------|----------|-----------|-----------------
| `all_varchar`       | Boolean  | Optional |  Skip type detection and assume all columns are VARCHAR
| `allow_quoted_nulls`| Boolean  | Optional |  Allow conversion of quoted values to NULL
| `decimal_separator` | String   | Optional |  The decimal separator for numbers
| `delimiter`         | String   | Optional |  The character that separates columns
| `description`       | String   | Optional |  A description of the format
| `escape`            | String   | Optional |  The escape character for quoted values
| `filename`          | Boolean  | Optional |  Include an extra filename column
| `force_not_null`    | List     | Optional |  List of columns that should not be converted to NULL
| `header`            | Boolean  | Optional |  Whether the file contains a header row
| `ignore_errors`     | Boolean  | Optional |  Ignore parsing errors
| `max_line_size`     | Number   | Optional |  Maximum line size in bytes
| `new_line`          | String   | Optional |  New line character(s) ('\r', '\n', or '\r\n')
| `normalize_names`   | Boolean  | Optional |  Remove non-alphanumeric characters from column names
| `null_padding`      | Boolean  | Optional |  Pad missing columns with NULL values
| `null_str`          | List     | Optional |  String(s) that represent NULL values
| `quote`             | String   | Optional |  The quoting character
| `sample_size`       | Number   | Optional |  Number of sample rows for auto-detection
| `timestamp_format`  | String   | Optional |  Format for parsing timestamps


#### JSONL Format
The `jsonl` (JSON Lines) format is used for parsing JSON data where each line is a valid JSON object.

Example:
```hcl
format "jsonl" "custom_jsonl" {
  description = "JSON Lines format with one JSON object per line"
}
```

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `description`| String   | Optional  | A description of the format
