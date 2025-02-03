---
title:  partition
---

# partition

The [partition](/docs/manage/partition) block defines the set of log rows, in a plugin-defined Tailpipe table, that come from a specified [source](/docs/manage/source). A given Tailpipe table, like `aws_cloudtrail_log`, can include multiple partitions that use one or several `source` types. 

## Arguments

| Argument | Type   | Optional? | Description
|----------|--------|-----------|-----------------
| `filter` | String | Optional  | A SQL `where` clause condition to filter log entries. Supports expressions using table columns.

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
