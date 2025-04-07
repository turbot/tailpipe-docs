---
title:  partition
---

# partition

The [partition](/docs/manage/partition) block defines the set of log rows, in a plugin-defined Tailpipe table, that come from a specified [source](/docs/manage/source). A given Tailpipe table, like `aws_cloudtrail_log`, can include multiple partitions that use one or several `source` types. 

## Arguments

| Argument | Type   | Optional? | Description
|----------|--------|-----------|-----------------
| `source` | Block  | Required  | one or more [sources](#source) from which to collect data.
| `filter` | String | Optional  | A SQL `where` clause condition to filter log entries. Supports expressions using table columns.



## source

A partition acquires data from one or more sources. The `source` block enables to to specify the type and location of the source data, as well as the [`connection`](reference/config-files/connection) to use to connect to it.


```hcl
partition "aws_cloudtrail_log" "s3_bucket_us_east_1" {
  source "aws_s3_bucket" {
    connection  = connection.aws.account_a
    bucket      = "aws-cloudtrail-logs-account-a"
    file_layout = "AWSLogs/(%{DATA:org_id}/)?%{NUMBER:account_id}/CloudTrail/us-east-1/%{DATA}.json.gz"    
  }
}
```

The block label denotes the source type - `aws_s3_bucket`, `file`, etc.  The source types are defined in plugins, and you can view them with the `tailpipe source list` command.  The Tailpipe Hub provides [extended documentation and examples](https://hub.tailpipe.io/plugins/turbot/aws/sources/aws_s3_bucket) for plugin sources as well.


### file_layout

The arguments to the `source` vary by type, many source types may include the `file_layout` argument.  The `file_layout` specifies a [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html#_grok_basics) pattern that defines the log file directory structure.  The `file_layout` is usually optional; the plugin will provide a default that works for the most common case. 

The `file_layout` is not merely used to locate log files, but also to describe any fields that appear in the path that are meaningful to the collection process.  Log files are often stored in a predictable, meaningful path structure that can be used to identify the log file dates, accounts, etc.  Date and time fields are particularly important, as Tailpipe uses them to maintain collection state so that it can resume collection from its previous checkpoint.  When setting `file_layout`, make sure you identify any date or time fields (`year`, `month`, `day`, `hour`, `minute`, `second`) that appear in the path, for example:

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
    file_layout = "AWSLogs/(%{DATA:org_id}/)?%{NUMBER:account_id}/CloudTrail/us-east-1/%{DATA}.json.gz"    
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
