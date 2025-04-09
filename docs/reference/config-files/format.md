---
title: format
---

# format

The `format` block enables you to define source formats for tables and sources.  Formats describe the layout of the source data so that it can be collected into a table.


```hcl
format "regex" "custom_log" {
  layout = `^(?P<timestamp>[\d-]+\s+[\d:.]+)\s+(?P<pid>\d+)\s+(?P<log_level>\w+)\s+(?P<component>[\w._-]+)`
}
```

> [!TIP]
> Use backticks (<code>`</code>) to delimit the <code>layout</code>.  Tailpipe treats anything in backticks as a non-interpolated string, so you don't have to escape quotes, backslashes, etc.

## Formats

You can define a format with the `format` block:

```hcl
format "regex" "custom_log" {
  layout = `^(?P<timestamp>[\d-]+\s+[\d:.]+)\s+(?P<pid>\d+)\s+(?P<log_level>\w+)\s+(?P<component>[\w._-]+)`
}
```
Format blocks have 2 labels:
- The [format type](#format-types).  This can be a [core format type](#core-plugin-formats) or any format type in any installed plugin
- A name for the format


Plugins may also export preset formats which may be referenced by name.    For example, the [Nginx plugin](https://hub.tailpipe.io/plugins/turbot/nginx) provides the `nginx_access_log.combined` format which defines the Nginx default combined log format:
```
$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent"
```

You can list and view details of both your custom formats and the plugin preset formats using the introspection `tailpipe format list` and `tailpipe format show` commands.



## Format Types

The format type defines the parsing mechanism which should be used. The properties of the `format` are specific to the format type.

Formats types are implemented by plugins. A number of "generic" formats types are [provided by the `core` plugin](#core-plugin-formats), which is included in every Tailpipe installation.  These core format types provide a mechanism for describing file layouts using general-purpose syntax such as regular expressions, Grok, and JSONL. 
 
Any plugin may include a format type to simplify describing the layout of log files specific to the plugin using its "native" syntax.  For example, the [Nginx plugin](https://hub.tailpipe.io/plugins/turbot/nginx) provides the `nginx_access_log` format type.  When using the `nginx_access_log` format, you can specify the `layout` using the same [Nginx log_format](https://nginx.org/en/docs/http/ngx_http_log_module.html#log_format) as you use in your Nginx configuration files:

```hcl
format "nginx_access_log" "my_format" {
  layout = `$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_time`
}
```

You can discover the installed format types with the introspection `tailpipe format list` command.


### Core Plugin Formats

#### Grok Format
The `grok` format is used for parsing log lines using [Grok patterns](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html#_grok_basics), which are a way to parse log lines into structured data.

```hcl
format "grok" "custom_log" {
  layout = `%{TIMESTAMP_ISO8601:time_local} - %{NUMBER:event_id} - %{WORD:user} - [%{REGION:location}] \"%{DATA:message}\" %{WORD:severity}`
  patterns = {
    "REGION" = "[a-zA-Z0-9\\-]+"
  }
}
```

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `layout`     | String   | Required  | The Grok pattern that defines how to parse the log line
| `patterns`   | Map      | Optional  | A map of custom Grok patterns that can be referenced in the layout.  This is optional, and the [standard patterns](https://github.com/elastic/go-grok?tab=readme-ov-file#default-set-of-patterns) are available out-of-the-box.
| `description`| String   | Optional  | A description of the format

> [!TIP]
> Use the [Grok Debugger](https://grokdebugger.com/) to help create and test your grok expressions. 


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

> [!TIP]
> Use the [RegEx 101](https://regex101.com/) to help create and test your regular expressions. 

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
