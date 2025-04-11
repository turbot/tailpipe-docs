---
title:  partition
---

# partition

A [partition](/docs/collect/configure#partitions) represents data gathered from a [source](/docs/collect/configure#sources). A given Tailpipe table, like `aws_cloudtrail_log`, can include multiple partitions. Partitions are defined in HCL and are required for [collection](/docs/collect/collect).  


```hcl
partition "aws_cloudtrail_log" "test" {

  source "aws_s3" {
    connection = connection.aws.logs
    bucket     = "my-logs-bucket"
  }
  
}
```

The partition has two labels:

1. The [table](#tables) name. The table name is meaningful and must match a table name for an installed [plugin](/docs/collect/plugins) or a custom [table](/docs/reference/config-files/table). 

2. A partition name.  The partition name must be unique for all partitions in a given table (though different tables may use the same partition names).  



## Arguments

| Argument | Type   | Optional? | Description
|----------|--------|-----------|-----------------
| `source` | Block  | Required  | a [source](#source) from which to collect data.
| `filter` | String | Optional  | A SQL `where` clause condition to filter log entries. Supports expressions using table columns.



## source

A partition acquires data from a source. The `source` block specifies the type and location of the source data, as well as the [`connection`](/docs/reference/config-files/connection) to use to connect to it.


```hcl
partition "aws_cloudtrail_log" "s3_bucket_us_east_1" {
  source "aws_s3_bucket" {
    connection  = connection.aws.account_a
    bucket      = "aws-cloudtrail-logs-account-a"
    file_layout = `AWSLogs/(%{DATA:org_id}/)?%{NUMBER:account_id}/CloudTrail/us-east-1/%{DATA}.json.gz`   
  }
}
```

The block label denotes the source type - `aws_s3_bucket`, `file`, etc.  The source types are defined in plugins, and you can view them with the `tailpipe source list` command.  The [`file` source](#file-source) is provided by the `core` plugin, which is included in every Tailpipe installation. 


### Source Arguments
The `source` arguments vary by source type.  The Tailpipe Hub provides [extended documentation and examples](https://hub.tailpipe.io/plugins/turbot/aws/sources/aws_s3_bucket) for plugin sources.  

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `connection` |  [connection](/docs/reference/config-files/connection) reference | Varies by source type | The [connection](/docs/reference/config-files/connection) to use to connect to the source.  This is required for most sources except `file`.   
| `file_layout`| String  | Optional  | The Grok pattern that [defines the log file structure](#file_layout).  `file_layout` is optional if not provided all files at the path(s) from paths will be collected. 
| `format`     | [format](/docs/reference/config-files/format) reference | Optional  | The default format of the source data. This must refer to either a `format` block or a format preset defined by a plugin. If no `format` is specified, the default for the table will be used.
| `patterns`   | Map      | Optional  | A map of custom Grok patterns that can be referenced in the `file_layout`.  This is optional, and the [standard patterns](https://github.com/elastic/go-grok?tab=readme-ov-file#default-set-of-patterns) are available out-of-the-box.



#### format

While the arguments to `source` vary by type, every `source` supports specifying a `format` for the source.  This must refer to either a `format` block or a format preset defined by a plugin.  If no `format` is specified, the default for the table will be used.  

```hcl
table "my_custom_table" {
  column "tp_timestamp" {
    source = "time_local"
  }
}

format "regex" "example_format" {
  layout = `^(?P<remote_addr>[^ ]*) - (?P<remote_user>[^ ]*) \[(?P<time_local>[^\]]*)\] "(?P<request_method>\S+)(?: +(?P<request_uri>[^ ]+))?(?: +(?P<server_protocol>\S+))?" (?P<status>[^ ]*) (?P<body_bytes_sent>[^ ]*) "(?P<http_referer>.*?)" "(?P<http_user_agent>.*?)"`
}

partition "my_custom_table" "my_partition_name" {
  source "file"  {
    format      = format.regex.example_format
    paths       = ["/Users/graza/tailpipe_data/nginx_access_logs"]
    file_layout = `%{YEAR:year}%{MONTHNUM:month}%{MONTHDAY:day}_%{DATA}.log`
  }
}
```

#### file_layout

The arguments to the `source` vary by type, but many source types include the `file_layout` argument.  The `file_layout` specifies a [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html#_grok_basics) pattern that defines the log file directory structure.  The `file_layout` is usually optional; the plugin will provide a default that works for the most common case. 

The `file_layout` is not merely used to locate log files but also to describe any fields that appear in the path that are meaningful to the collection process.  Log files are often stored in a predictable, meaningful path structure that can be used to identify the log file dates, accounts, etc.  Date and time fields are particularly important, as Tailpipe uses them to maintain collection state so that it can resume collection from its previous checkpoint.  When setting `file_layout`, make sure you identify any date or time fields (`year`, `month`, `day`, `hour`, `minute`, `second`) that appear in the path, for example:

```hcl
partition "aws_cloudtrail_log" "my_logs_custom_path" {
  source "aws_s3_bucket" {
    connection  = connection.aws.logging_account
    bucket      = "aws-cloudtrail-logs-bucket"
    file_layout = `CustomLogs/Dev/%{YEAR:year}/%{MONTHNUM:month}/%{MONTHDAY:day}/%{DATA}.json.gz`
  }
}
```

Refer to the documentation for your table on the [Tailpipe Hub](https://hub.tailpipe.io/) for examples.

> [!TIP]
> Use backticks (<code>`</code>) to delimit the <code>file_layout</code>.  Tailpipe treats anything in backticks as a non-interpolated string, so you don't have to escape quotes, backslashes, etc.


#### file source

The `file` source enables you to collect files on your local filesystem.

```hcl
source "file" {
  paths       = ["/my/logs/April", "/my/logs/May"]
  file_layout = `%{MY_PATTERN}.log`

  patterns = {
    "MY_PATTERN": `%{YEAR:year}%{MONTHNUM:month}%{MONTHDAY:day}-blah-%{NUMBER}`
  }
}
```

| Argument     | Type     | Optional? | Description
|--------------|----------|-----------|-----------------
| `paths`      | String   | Required  | The path to the files to collect.
| `file_layout` | String  | Optional  | The Grok pattern that [defines the log file structure](#file_layout).  `file_layout` is optional if not provided all files at the path(s) from paths will be collected.
| `format`     | [format](/docs/reference/config-files/format) reference | Optional  | The default format of the source data. This must refer to either a `format` block or a format preset defined by a plugin. If no `format` is specified, the default for the table will be used.
| `patterns`   | Map      | Optional  | A map of custom Grok patterns that can be referenced in the `file_layout`.  This is optional, and the [standard patterns](https://github.com/elastic/go-grok?tab=readme-ov-file#default-set-of-patterns) are available out-of-the-box.

<!--
| `description`| String   | Optional  | A description of the source
-->



## Examples

You can define a partition that uses the `aws_s3_bucket` type to collect all the CloudTrail log files from an S3 bucket:

```hcl
partition "aws_cloudtrail_log" "s3_bucket_all" {
  source "aws_s3_bucket" {
    connection = connection.aws.account_a
    bucket     = "aws-cloudtrail-logs-account-a"
  }
}
```

You can use the `filter` argument to exclude specific log entries with expressions using table columns:

```hcl
partition "aws_cloudtrail_log" "ec2_write_events" {
  # Only save EC2 write events
  filter = "not read_only and event_source = 'ec2.amazonaws.com'"

  source "aws_s3_bucket" {
    connection = connection.aws.account_a
    bucket     = "aws-cloudtrail-logs-account-a"
  }
}
```

You can use the `file_layout` argument to scope the set of collected log files using [grok patterns](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html?utm_source=chatgpt.com#_grok_basics). This `source` block matches only `us-east-1` rows.

```hcl
partition "aws_cloudtrail_log" "s3_bucket_us_east_1" {
  source "aws_s3_bucket" {
    connection  = connection.aws.account_a
    bucket      = "aws-cloudtrail-logs-account-a"
    file_layout = `AWSLogs/(%{DATA:org_id}/)?%{NUMBER:account_id}/CloudTrail/us-east-1/%{DATA}.json.gz`  
  }
}
```

Another `source` type, `file`, enables you to collect from local log files that you've downloaded. This partition collects the [flaws.cloud](https://flaws.cloud) files.

```hcl
partition "aws_cloudtrail_log" "flaws" {
  source "file"  {
    paths = ["/Users/dboeke/flaws/flaws_cloudtrail_logs"]    
  }
}
```
