---
id: learn
title: Learn Tailpipe
slug: /
---


# Learn Tailpipe

Tailpipe is a high-performance data collection and querying tool that makes it easy to collect, store, and analyze log data. With Tailpipe you can:

- Collect logs from various sources and store them efficiently in parquet files
- Query your data using familiar SQL syntax using Tailpipe (or DuckDB!)
- Create filtered views of your data
- Join log data with other data sources for enriched analysis

> [!NOTE]
> this list is provisional, needs discussion, it's the first thing people see.
> the second two items may be too advanced for this context?
> if so, what are more basic things to call out here?
## Install the AWS Plugin

This tutorial uses the AWS plugin to demonstrate collecting and analyzing Cloudtrail logs. First, [download and install Tailpipe](/downloads).

```bash+macos
brew install turbot/tap/tailpipe
```

```bash+linux
sudo /bin/sh -c "$(curl -fsSL https://tailpipe.io/install/tailpipe.sh)"
```

Then install the plugin:

```bash
tailpipe plugin install aws
```

Out of the box, Tailpipe will use the default AWS credentials from your credential file and/or environment variables; if you can run `aws ec2 describe-vpcs`, for example, then you should be able to run the examples. 

The AWS plugin documentation provides additional examples to [configure your credentials](https://hub.tailpipe.io/plugins/turbot/aws#configuring-aws-credentials), and you can even configure Tailpipe to query [multiple accounts](https://tailpipe.io/docs#:~:text=tailpipe%20to%20query-,multiple%20accounts,-and%20multiple%20regions) and [multiple regions](https://tailpipe.io/docs#:~:text=multiple%20accounts%20and-,multiple%20regions).

> [!NOTE]
> Should we provide a file-source alternative for those who don't have a live AWS account or lack access to cloudtrail logs in one? 
> We could provide some dummy data in a tailpipe-samples repo.

## Configure Data Collection

Tailpipe uses HCL configuration files to define what data to collect. Here's a configuration that uses the `aws_s3_bucket` source.

```
 connection "aws" "dev" {
  profile = "SSO-Admin-605...13981"
  regions = ["*"]
}

partition "aws_cloudtrail_log" "dev" {
  source "aws_s3_bucket" {
    connection = connection.aws.dev
    bucket     = "aws-cloudtrail-logs-6054...81-fe67"
    prefix     = "AWSLogs/6054...81/CloudTrail/us-east-1/2024/12"
    region     = "us-east-1"
    extensions = [".gz"]
  }
}
```

Put this in a file, e.g. `aws.tpc`, and save it to `~/.tailpipe/config`.

This configuration tells Tailpipe to collect Cloudtrail logs from the specified S3 bucket. The configuration defines:

- A connection that enables Tailpipe to access the logs

- A plugin-defined table, `aws_cloudtrail_log`

- A partition within the table, `dev`

## Collect Data

Now let's collect the logs:

```bash
tailpipe collect aws_cloudtrail_log
```

This command will:

- Acquire compressed (.gz) logs files from the bucket

- Uncompress them

- Parse all the .json log files and map fields of each line to the plugin-defined schema

- Store the data in parquet files organized by date

## Query Your Data

Tailpipe provides an interactive SQL shell for analyzing your collected data. Let's look at some examples of what you can do.

### Check the range of the data

This query finds the oldest and newest log lines.

```bash
tailpipe query "select min(tp_date), max(tp_date) from aws_cloudtrail_log"
```

### List most common source IP addresses

This query finds the top 10 IPs.

```bash
tailpipe query "select tp_source_ip, count(*) as count from aws_cloudtrail_log group by tp_source_ip order by count desc"
```

### List event types for one day

This query lists Cloudtrail event types for a specified day.

```bash
tailpipe query "select distinct event_type from aws_cloudtrail_log where tp_date = '2024-11-07'"
```

Because we specified `tp_date = '2024-11-07'`, Tailpipe only needs to read the a small subset of the parquet files created by the collection process. Similarly, if you define another partition (e.g. `prod`), you can use the partition name to scope queries to just the subset of files for that partition.

## What's Next?

We've demonstrated basic log collection and analysis with Tailpipe. Here's what to explore next:

- [Discover more plugins on the Hub →](https://hub.tailpipe.io/plugins)
- [Discover pre-built benchmarks and dashboard for popular log formats →](https://hub.tailpipe.io/mods)
- [Join #tailpipe on Slack →](https://turbot.com/community/join)

