---
title:  FAQ
---

# FAQ

## Why does collection report fewer rows than expected?

Tailpipe maintains collection state, so you'll only see new rows. 

## How do I reset a table?

If you want to reset and collect everything, you can use  `rm -rf ~/.tailpipe/internal/collection/default/*`. You might want to do this if you're building your own plugin and changing the schema.

>[!NOTE]
> What's the story for published plugins that change schema?

## Can I use DuckDB to query my Tailpipe tables?

Yes. Use `tailpipe connect` to acquire a unique connection string. For example:

```bash
$ tailpipe connect
/home/jon/.tailpipe/data/default/tailpipe_20241224111847.db
```

Then connect to it:

```bash
$ duckdb /home/jon/.tailpipe/data/default/tailpipe_20241224111847.db
D .tables
aws_cloudtrail_log  pipes_audit_log
```

## Can I define more than one partition per table?

Yes. In this example, the `aws_cloudtrail_log` table has two partitions, one for each of two AWS accounts.

```hcl
 connection "aws" "12345" {
  profile = "SSO-12345"
  regions = ["*"]
}

 connection "aws" "6789" {
  profile = "SSO-6789"
  regions = ["*"]
}

partition "aws_cloudtrail_log" "12345" {
  source "aws_s3_bucket" {
    connection = connection.aws.12345
    bucket     = "aws-cloudtrail-logs-12345"
    file_layout = "AWSLogs/%{NUMBER:account_id}/%{DATA}.json.gz"
  }

partition "aws_cloudtrail_log" "6789" {
  source "aws_s3_bucket" {
    connection = connection.aws.6789
    bucket     = "aws-cloudtrail-logs-6789"
    file_layout = "AWSLogs/%{NUMBER:account_id}/%{DATA}.json.gz"
  }
```

## Can I define multiple sources for a table?

Yes. In this example, the `aws_cloudtrail_log` table has one partition that encompasses two AWS accounts.

```hcl
 connection "aws" "12345" {
  profile = "SSO-12345"
  regions = ["*"]
}

 connection "aws" "6789" {
  profile = "SSO-6789"
  regions = ["*"]
}

partition "aws_cloudtrail_log" "cloudtrail_all" {
  source "aws_s3_bucket" {
    connection = connection.aws.12345
    bucket     = "aws-cloudtrail-logs-12345-2755fe67"
    file_layout = "AWSLogs/%{NUMBER:account_id}/%{DATA}.json.gz"
  }

  source "aws_s3_bucket" {
    connection = connection.aws.6789
    bucket     = "aws-cloudtrail-logs-6789-2755fe67"
    file_layout = "AWSLogs/%{NUMBER:account_id}/%{DATA}.json.gz"
  }
```

## What partition indexes are available for a table?

The `tp_index` value depends on how you have configured it in your [partition config](/docs/reference/config-files/partition). By default, `tp_index` is set to `"default"`, but you can configure it to specify a column whose value should be used as the partition index, as makes sense for the data. For AWS tables, you might set it to `account_id`.
