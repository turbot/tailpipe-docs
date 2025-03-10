---
id: learn
title: Learn Tailpipe
slug: /
---


# Learn Tailpipe

Tailpipe is a high-performance data collection and querying tool that makes it easy to collect, store, and analyze log data. With Tailpipe you can:

- [Collect](/docs/manage/collection) logs from various sources and store them efficiently
- [Query](/docs/query) your data with familiar SQL syntax using Tailpipe (or DuckDB!)
- Use [Powerpipe](https://powerpipe.io) to visualize your logs and run detections

## Prerequisites

This tutorial uses the [AWS plugin](https://hub.tailpipe.io/plugins/turbot/aws) to demonstrate collection and analysis of Cloudtrail logs. First, [download and install Tailpipe](/downloads).

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

## Configure data collection

Tailpipe uses HCL configuration files to define what data to collect. You will need to define a [connection](/docs/manage/connection) that governs how Tailpipe accesses logs. For example:

```hcl
connection "aws" "admin" {
  profile = "SSO-Admin-605...13981"
}
```

Tailpipe can use the default AWS credentials from your credential file and/or environment variables; if you can run `aws ls s3`, for example, then you should be able to collect CloudTrail logs. The AWS plugin [documentation](https://hub.tailpipe.io/plugins/turbot/aws) describes other access patterns.

You will also need to define a [partition](/docs/manage/partition) which refers to a plugin-defined table (`aws_cloudtrail_log`) that describes the data found in each line of a Cloudtrail log, and a [source](/docs/manage/source) that governs how Tailpipe acquires the data that populates the partition.

```hcl
partition "aws_cloudtrail_log" "prod" {
  source "aws_s3_bucket" {
    connection  = connection.aws.admin
    bucket      = "aws-cloudtrail-logs-6054...81-fe67"
    file_layout = "AWSLogs/%{NUMBER:account_id}/%{DATA}.json.gz"
  }
}
```

Create a file, e.g. `~/.tailpipe/config/aws.tpc`, with a `connection` and `partition` block similar to these examples.  

> [!NOTE]
> If you don't have access to live Cloudtrail logs, you can use the [flaws.cloud](http://flaws.cloud/) sample logs. To get them:
> ```bash
> mkdir ~/flaws
> cd ~/flaws
> curl -O https://summitroute.com/downloads/flaws_cloudtrail_logs.tar
> tar xvf flaws_cloudtrail_logs.tar
> ```
> To source the log data from the `.gz` file extracted from the tar file, your `aws.tpc` file won't include a `connection` block. Its `partition` block will follow this format:

> ```hcl
> partition "aws_cloudtrail_log" "flaws" {
> source "file" {
>    paths       = ["/Users/dboeke/flaws/flaws_cloudtrail_logs"]
>  }
>}
>```

## Collect log data

Now let's collect the logs:

```bash
tailpipe collect aws_cloudtrail_log  --from 2016-01-01
```


Tailpipe will download the files from the source, decompress and parse them, and add the data to the Tailpipe database in the [standard hive file structure](/docs/manage/hive).

![](/learn/collection.png)


## Query your logs

Tailpipe provides an interactive SQL shell for analyzing your collected data. Run `tailpipe query` to start the query shell. To see the table that was created:

```bash
$ tailpipe query
> .inspect
Table              Plugin          
aws_cloudtrail_log aws@0.1.0
```

You can count the records in the table:

```bash
select
  count(*)
from 
  aws_cloudtrail_log
```
or find the oldest and newest records:

```bash
select 
  min(tp_date), max(tp_date)
from 
  aws_cloudtrail_log
```

This query finds the top 10 IPs:

```bash
select 
  tp_source_ip, count(*) as count
from
   aws_cloudtrail_log
group by
  tp_source_ip order by count desc
```

This query lists Cloudtrail event types for a specified day:

```bash
select distinct 
  event_type 
from 
  aws_cloudtrail_log
where 
  tp_date = '2024-11-07'
```

Because we specified `tp_date = '2024-11-07'`, Tailpipe only needs to read one of many files created by the collection process. 

## What's next?

We've demonstrated basic log collection and analysis with Tailpipe. Here's what to explore next:

- [Visualize your Tailpipe log data with Powerpipe Dashbaords](https://powerpipe.io/docs/learn/tailpipe)  
- [Discover more plugins on the Hub →](https://hub.tailpipe.io/plugins)
- [Discover pre-built benchmarks and dashboard for popular log formats →](https://hub.powerpipe.io/?engines=tailpipe)
- [Join #tailpipe on Slack →](https://turbot.com/community/join)
