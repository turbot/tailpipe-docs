---
title: Sources
---

# Sources

A partition acquires data from one or more **sources**.  Often a source will connect to a resource via a [connection](/docs/reference/config-files/connection) which specifies the credentials and account scope.  The source is typically more specific than the connection though. For example, a connection that provides the ability to interact with an AWS account may support several sources that use that connection but provide logs from different services and locations, for example two AWS S3 sources from two different S3 buckets.

A plugin's source mechanism is responsible for:

- turning raw data into rows

- initiating file transfers / requests

- downloading / copying raw data

- unzipping/untaring, etc

- incremental transfer / tracking, retrying, etc

- source-specific filtering for sources that support them, e.g. [Cloudwatch log filters](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)

Sources are defined as sub-blocks in a *partition*. 

```hcl
partition "aws_cloudtrail_log" "test" {
  source "aws_s3_bucket" {
    connection    = connection.aws.logs
    bucket        = "my-logs-bucket"
    file_layout   = "{%DATA}.json.gz"
  }
  
  source "file" {
    path       = "/path/to/files"
    file_layout   = "{%DATA}.json.gz"
  }
}
```

Standard source types, like `aws_s3_bucket` and `file`, have an HCL shape that is consistent across all the partitions, and a standard set of arguments. 

>[!NOTE]
> is this still true, given file_layout?

However the *interpretation* of the arguments, and the *behavior* of the source, is plugin-dependent. For instance, the `aws_s3_source` for the `aws_cloudtrail_log` table makes Cloudtrail-specific assumptions about the key prefix structure and file names.



