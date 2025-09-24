---
title:  FAQ
---

# FAQ

## Can I use DuckDB to query my Tailpipe tables?

Yes. Use `tailpipe connect` to get the path to a SQL script. For example:

```bash
$ tailpipe connect
/Users/pskrbasu/.tailpipe/data/default/tailpipe_init_20250923222209.sql
```

Then connect to it:

```bash
$ duckdb -init /Users/pskrbasu/.tailpipe/data/default/tailpipe_init_20250923222209.sql
D .tables
aws_cloudtrail_log  pipes_audit_log
```

> [!NOTE]
> To ensure compatibility with DuckLake features, make sure you’re using DuckDB version 1.4.0 or later.

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
}

partition "aws_cloudtrail_log" "6789" {
  source "aws_s3_bucket" {
    connection = connection.aws.6789
    bucket     = "aws-cloudtrail-logs-6789"
    file_layout = "AWSLogs/%{NUMBER:account_id}/%{DATA}.json.gz"
  }
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

## What Linux distributions and versions are officially supported by Tailpipe?

Tailpipe requires glibc version 2.29 or higher and libstdc++ (GCC runtime) with symbol version GLIBCXX_3.4.30 or higher. It will not function on systems with an older glibc or an older libstdc++ runtime.

Tailpipe is tested on the latest versions of Linux LTS distributions. While it may work on other distributions with the required glibc and libstdc++ versions, official support and testing are limited to the following:


| Distribution       | Version | glibc Version | Notes                                                   |
|--------------------|---------|---------------|---------------------------------------------------------|
| Ubuntu LTS         | 24.04   | 2.39          |                                                         |
| Ubuntu             | 22      | 2.35          | To cover Windows WSL2, which may be behind              |
| CentOS (Stream)    | 10      | 2.39          |                                                         |
| RHEL               | 10      | 2.39          |                                                         |
| Amazon Linux       | 2023    | 2.34          |                                                         |