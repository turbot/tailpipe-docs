---
id: learn
title: Learn Tailpipe
slug: /
---


# Learn Tailpipe

Tailpipex is a high-performance data collection and querying tool that makes it easy to collect, store, and analyze log data. With Tailpipe you can:

- Collect logs from various sources and store them efficiently
- Query your data with familiar SQL syntax using Tailpipe (or DuckDB!)
- Use [Powerpipe](https://powerpipe.io) to visualize your logs and run detections 

This tutorial uses the AWS plugin to demonstrate collection and analysis of Cloudtrail logs. First, [download and install Tailpipe](/downloads).

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

## Configure Data Collection

Tailpipe uses HCL configuration files to define what data to collect. Here's a configuration that collects Cloudtrail logs. 

```
connection "aws" "sample" {
  profile = "SSO-Admin-605...13981"
}

partition "aws_cloudtrail_log" "sample" {
  source "aws_s3_bucket" {
    connection = connection.aws.dev
    bucket     = "aws-cloudtrail-logs-6054...81-fe67"
  }
}
```

The `connection` governs how Tailpipe accesses logs. Tailpipe can use the default AWS credentials from your credential file and/or environment variables; if you can run `aws ls s3`, for example, then you should be able to run the examples. The AWS plugin [documentation](https://hub.tailpipe.io/plugins/turbot/aws) describes other access patterns.


The `partition` refers to a plugin-defined table, `aws_cloudtrail_log`, with a schema that describes the data found in each line of a Cloudtrail log. 

Put this HCL code in a file, e.g. `aws.tpc`, and save it to `~/.tailpipe/config`.

Note: If you don't have any access to live Cloudtrail logs, you can use the [flaws.cloud](http://flaws.cloud/) sample logs. To get them:

```
mkdir ~/flaws
cd ~/flaws
wget https://summitroute.com/downloads/flaws_cloudtrail_logs.tar
tar xvf flaws_cloudtrail_logs.tar
```

To configure:

```
partition "aws_cloudtrail_log" "sample" {
 source "file_system" {
    paths = ["~/flaws"]
    extensions = ["*.gz"]
  }
}
```

## Collect Data

Now let's collect the logs:

```bash
tailpipe collect aws_cloudtrail_log.sample
```

This command will:

- Acquire compressed (.gz) log files

- Uncompress them

- Parse all the .json log files and map fields of each line to the plugin-defined schema

- Store the data in files organized by date

By default, Tailpipe collects only the most recent 7 days of log data.

## View the table

>[!NOTE]
> use .inspect here if available, else tailpipe table list

## Query Your Data

Tailpipe provides an interactive SQL shell for analyzing your collected data. You can count the records in the table.

```bash
tailpipe query "count(*) from aws_cloudtrail_log"
```

or find the oldest and newest records.

```bash
tailpipe query "select min(tp_date), max(tp_date) from aws_cloudtrail_log"
```

This query finds the top 10 IPs.

```bash
tailpipe query "select tp_source_ip, count(*) as count from aws_cloudtrail_log group by tp_source_ip order by count desc"
```

This query lists Cloudtrail event types for a specified day.

```bash
tailpipe query "select distinct event_type from aws_cloudtrail_log where tp_date = '2024-11-07'"
```

Because we specified `tp_date = '2024-11-07'`, Tailpipe only needs to read the a small subset of the files created by the collection process. 

## What's Next?

We've demonstrated basic log collection and analysis with Tailpipe. Here's what to explore next:

- [Discover more plugins on the Hub →](https://hub.tailpipe.io/plugins)
- [Discover pre-built benchmarks and dashboard for popular log formats →](https://hub.powerpipe.io/?engines=tailpipe)
- [Join #tailpipe on Slack →](https://turbot.com/community/join)

