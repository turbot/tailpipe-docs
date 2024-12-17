---
title: Sources
---

# Sources
A partition has one or more **sources**.  
 

**Sources** are the mechanism for getting logs.  Usually, a source will connect to a resource via a **connection** -- the connection specifies the credentials and account scope.   The source is usually more specific than the connection though;  a *connection* provides the ability to interact with an AWS account, and you may have several *sources* that use that connection but provide logs from different services and locations - perhaps an aws s3 source in one bucket, another aws s3 source in another bucket, and a 3rd source that get logs from a Cloudwatch Logs log group.


The source is responsible for:
- turning raw data into rows
- initiating file transfers / requests
- downloading / copying raw data
- unzipping/untaring, etc
- incremental transfer / tracking, retrying, etc
  - The source receives configuration from the table using it to understand the directory structure, file name semantics, and source data format
- source-specific filtering for sources that support them - eg [Cloudwatch log filters](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)

The source does NOT:
  - modify or standardize the data (the partition does that)
  - generalized filtering based on row contents (the partition does that, though the SDK may provide tools for this)

Sources are defined as sub blocks in a *partition*. 

```hcl
partition "aws_cloudtrail_log" "test" {

  source "aws_s3" {
    connection = connection.aws.logs
    bucket = "my-logs-bucket"
    prefix = "optional/path"
  }
  source "file" {
    path = "/path/to/files"
  }
  source "aws_sqs" {
    connection = connection.aws.logs
    queue_url = "https://my-queue"
  }
  source "aws_cloudwatch" {
    connection = connection.aws.logs
    log_group_arn = "arn:..."
  }
}

```

There are "standard" source types, like `aws_s3` and `file`.  The HCL shape of these is consistent across all the partitions; they have a standard set of arguments.  Note that while the arguments are the same across all partitions, the *interpretation* of the arguments and the *behavior* of the source is somewhat plugin dependent.  For instance the s3 source for `aws_cloudtrail_log` will make assumptions about the key prefix structure, file names, and file strucure/schema that are specific to cloudtrail.



