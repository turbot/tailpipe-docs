---
id: learn
title: Learn Tailpipe
slug: /
---


# Learn Tailpipe

Tailpipe is a high-performance data collection and querying tool that makes it easy to collect, store, and analyze log data. With Tailpipe, you can:

- [Collect](/docs/collect/collect) logs from various sources and store them efficiently
- [Query](/docs/query) your data with familiar SQL syntax using Tailpipe (or DuckDB!)
- Use [Powerpipe](https://powerpipe.io) to visualize your logs and run detections

## Prerequisites

To get started, you will need to [download and install Tailpipe](/downloads).

```bash+macos
brew install turbot/tap/tailpipe
```

```bash+linux
sudo /bin/sh -c "$(curl -fsSL https://tailpipe.io/install/tailpipe.sh)"
```


This tutorial uses the [AWS plugin](https://hub.tailpipe.io/plugins/turbot/aws) to demonstrate the collection and analysis of Cloudtrail logs.  Let's install the plugin:

```bash
tailpipe plugin install aws
```

## Configure data collection

Tailpipe uses [HCL configuration files](/docs/reference/config-files) to define what data to collect.  Tailpipe will read *all* files with a `.tpc` extension from the configuration directory (`~/.tailpipe/config` by default), so you are free to arrange your config files as you please.  A common convention, however, is to create a file per plugin for all the resources that pertain to it. Create a file called `~/.tailpipe/config/aws.tpc` for this tutorial.

We will configure Tailpipe to download logs from an S3 bucket, so you will need to define a [connection](/docs/reference/config-files/connection) to provide credentials. The [AWS plugin documentation on the Tailpipe Hub](https://hub.tailpipe.io/plugins/turbot/aws#connection-credentials) provides examples.  Add a connection to your `aws.tpc` file, for example:

```hcl
connection "aws" "admin" {
  profile = "SSO-Admin-605...13981"
}
```

Now let's add a [partition](/docs/reference/config-files/partition) to our `aws.tpc` file.  A partition is used to collect data from a [source](/docs/reference/config-files/partition#source) and import it into a [table](/docs/collect/configure#tables).

```hcl
partition "aws_cloudtrail_log" "prod" {
  source "aws_s3_bucket" {
    connection  = connection.aws.admin
    bucket      = "aws-cloudtrail-logs-6054...81-fe67"
  }
}
```

Note that the partition has two labels.  The first one (`aws_cloudtrail_log`) is the name of a table to import the data into, and the second is a unique name for the partition. This table is defined by the AWS plugin that we installed earlier.  You can view the available tables with the `tailpipe table list` command.
```bash
tailpipe table list
```
```bash
NAME                                    PLUGIN                                         LOCAL SIZE    FILES    ROWS       DESCRIPTION
aws_alb_access_log                      hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS ALB access logs capture detailed information about the requests that are processed by an Application Load Balancer. This table provides a structured representation of the log data, including request and response details, client and target information, processing times, and security parameters.
aws_clb_access_log                      hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS CLB access logs capture detailed information about requests processed by a Classic Load Balancer, including client information, backend responses, and SSL details.
aws_cloudtrail_log                      hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS CloudTrail logs capture API activity and user actions within your AWS account.
aws_cost_and_usage_focus                hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS FOCUS 1.0 (Flexible, Optimized, and Comprehensive Usage and Savings) provides a detailed breakdown of AWS service usage and cost optimization opportunities. This table structures billing and usage data, including pricing details, commitment-based discounts, capacity reservations, and SKU-level pricing metrics. It enables cost tracking, commitment analysis, and efficient cloud financial management.
aws_cost_and_usage_report               hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS Cost and Usage Reports (CUR) provide a comprehensive breakdown of AWS service costs and usage. This table offers a structured view of billing data, including service charges, account-level spending, resource consumption, discounts, and pricing details. It enables cost analysis, budget tracking, and optimization insights across AWS accounts.
aws_cost_optimization_recommendation    hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS Cost Optimization Recommendations provide insights into opportunities to reduce AWS spending through various actions such as rightsizing, reserved instances, savings plans, and idle resource cleanup.
aws_guardduty_finding                   hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS GuardDuty findings provide detailed security alerts about potential threats and suspicious activities detected in your AWS environment. This table captures comprehensive information about each finding, including threat details, affected resources, and severity levels to help security teams identify and respond to potential security issues.
aws_nlb_access_log                      hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS NLB access logs capture detailed information about the connections that pass through a Network Load Balancer. This table provides a structured representation of the log data.
aws_s3_server_access_log                hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS S3 server access logs capture detailed information about the requests that are made to a bucket. This table provides a structured representation of the log data.
aws_vpc_flow_log                        hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS VPC Flow Logs capture information about IP traffic going to and from network interfaces in your VPC. This table provides detailed network traffic patterns, including source and destination IP addresses, ports, protocols, and traffic volumes, helping teams monitor network flows, troubleshoot connectivity issues, and detect security anomalies.
aws_waf_traffic_log                     hub.tailpipe.io/plugins/turbot/aws@latest      -             -        -          AWS WAF traffic logs record detailed web request data, helping analyze threats, monitor rule effectiveness, and improve security posture.
```

The partition also includes a `source` that uses the `connection` that we created earlier to download the logs from a `bucket`.  The block label denotes the source type (`aws_s3_bucket`). The source types are defined in plugins, and you can view them with the `tailpipe source list` command:
```bash
tailpipe source list
```
```
NAME             PLUGIN                                        DESCRIPTION
aws_s3_bucket    hub.tailpipe.io/plugins/turbot/aws@latest     
file             hub.tailpipe.io/plugins/turbot/core@latest   
```

The Tailpipe Hub provides [documentation and examples](https://hub.tailpipe.io/plugins/turbot/aws/sources/aws_s3_bucket) of how to configure the source.


> [!NOTE]
> If you don't have access to live Cloudtrail logs, you can use the [flaws.cloud](http://flaws.cloud/) sample logs instead. To get them:
> ```bash
> mkdir ~/flaws &&  cd ~/flaws
> curl -O https://summitroute.com/downloads/flaws_cloudtrail_logs.tar
> tar -xvf flaws_cloudtrail_logs.tar
> ```
> Then add a `partition` with `file` source to your `aws.tpc` file that point to the extracted files:
> ```hcl
> partition "aws_cloudtrail_log" "flaws" {
>   source "file" {
>     paths = ["/Users/dboeke/flaws/flaws_cloudtrail_logs"]
>   }
>}
>```

## Collect log data

Now, let's collect the logs:

```bash
tailpipe collect
```
```
Collecting logs for aws_cloudtrail_log.prod from 2025-04-04 

Artifacts:
  Discovered: 9,691 
  Downloaded: 9,691 29MB
  Extracted:  9,691

Rows:
  Received: 144,426
  Enriched: 144,426
  Saved:    144,110
  Filtered:     316

Files:
  Compacted: 66 => 9

Completed: 2m55s
```

Tailpipe will download the files from the source, decompress and parse them, and add the data to the Tailpipe database in the [standard hive file structure](/docs/collect/configure#hive-partitioning).

<!--
![](/learn/collection.png)
-->


## Query your logs

Tailpipe provides an interactive SQL shell for analyzing your collected data. Run `tailpipe query` to start the query shell. To see the table that was created:

```bash
$ tailpipe query
> .inspect
Table              Plugin          
aws_cloudtrail_log aws@0.8.1
```


You can [query](/docs/query) the logs using [SQL](/docs/sql). And you don't have to start from scratch — The Tailpipe Hub provides [hundreds of sample queries](https://hub.tailpipe.io/plugins/turbot/aws/queries?table=aws_cloudtrail_log)!


You can get statistics, like the count of records in the table:

```sql
select
  count(*)
from 
  aws_cloudtrail_log
```

or the most frequent error codes
```sql
select
  error_code,
  count(*) as event_count
from
  aws_cloudtrail_log
where
  error_code is not null
group by
  error_code
order by
  event_count desc;
```

or the activity by day.
```sql
select
  strftime(event_time, '%Y-%m-%d') AS event_date,
  count(*) AS event_count
from
  aws_cloudtrail_log
group by
  event_date
order by
  event_date asc;
```

Or look for suspicious activity, like `root` events
```sql
select
  event_time,
  event_name,
  source_ip_address,
  user_agent,
  aws_region,
  recipient_account_id as account_id
from
  aws_cloudtrail_log
where
  user_identity.type = 'Root'
order by
  event_time desc;
```

or unsuccessful AWS console login attempts
```sql
select
  event_time,
  event_name,
  user_identity.arn as user_arn,
  source_ip_address,
  user_agent,
  aws_region,
  recipient_account_id as account_id,
  additional_event_data
from
  aws_cloudtrail_log
where
  event_source = 'signin.amazonaws.com'
  and event_name = 'ConsoleLogin'
  and error_code is not null
order by
  event_time desc;
```



## What's next?

We've demonstrated basic log collection and analysis with Tailpipe. Here's what to explore next:

- [Visualize your Tailpipe log data with Powerpipe Dashboards](https://powerpipe.io/docs/learn/tailpipe)
- [Discover more plugins on the Hub →](https://hub.tailpipe.io/plugins)
- [Discover pre-built benchmarks and dashboard for popular log formats →](https://hub.powerpipe.io/?engines=tailpipe)
- [Join #tailpipe on Slack →](https://turbot.com/community/join)
