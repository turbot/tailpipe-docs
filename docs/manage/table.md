---
title: Tables
---

# Tables

Tables are implemented as DuckDB views over the Parquet files.  Tailpipe creates tables (that is, creates views in the `tailpipe.db` database) based on the data and metadata that it discovers in the [workspace](#workspaces), along with the filter rules.

When Tailpipe starts, it finds all the tables in the workspace according to the [hive directory layout](/docs/manage/partition#hive-partitioning).  For each schema, it adds a view for the table.  The view definitions will include qualifiers that implement the filter rules that are defined in the [schema definition](#schemas).


## Plugin Tables

Tailpipe [plugins](manage/plugin) define tables for common log sources and formats.  You don't need to define these tables; simply create one or more [partition](manage\partition) for the table and begin [collecting logs](manage/collection)!

You can see what tables are available with the `tailpipe plugin list` command.  The list will include any table defined in plugins that you have installed (even if you have not collected any data for them) as well as any [custom tables](#custom-tables) you have defined:

```bash
NAME                                    PLUGIN                                       LOCAL SIZE    FILES    ROWS       DESCRIPTION
aws_alb_access_log                      hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS ALB access logs capture detailed information about the requests that are processed by an Application Load Balancer. This table provides a structured representation of the log data, including request and response details, client and target information, processing times, and security parameters.
aws_clb_access_log                      hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS CLB access logs capture detailed information about requests processed by a Classic Load Balancer, including client information, backend responses, and SSL details.
aws_cloudtrail_log                      hub.tailpipe.io/plugins/turbot/aws@latest    23 MB         8        100,397    AWS CloudTrail logs capture API activity and user actions within your AWS account.
aws_cost_and_usage_focus                hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS FOCUS 1.0 (Flexible, Optimized, and Comprehensive Usage and Savings) provides a detailed breakdown of AWS service usage and cost optimization opportunities. This table structures billing and usage data, including pricing details, commitment-based discounts, capacity reservations, and SKU-level pricing metrics. It enables cost tracking, commitment analysis, and efficient cloud financial management.
aws_cost_and_usage_report               hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS Cost and Usage Reports (CUR) provide a comprehensive breakdown of AWS service costs and usage. This table offers a structured view of billing data, including service charges, account-level spending, resource consumption, discounts, and pricing details. It enables cost analysis, budget tracking, and optimization insights across AWS accounts.
aws_cost_optimization_recommendation    hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS Cost Optimization Recommendations provide insights into opportunities to reduce AWS spending through various actions such as rightsizing, reserved instances, savings plans, and idle resource cleanup.
aws_guardduty_finding                   hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS GuardDuty findings provide detailed security alerts about potential threats and suspicious activities detected in your AWS environment. This table captures comprehensive information about each finding, including threat details, affected resources, and severity levels to help security teams identify and respond to potential security issues.
aws_nlb_access_log                      hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS NLB access logs capture detailed information about the connections that pass through a Network Load Balancer. This table provides a structured representation of the log data.
aws_s3_server_access_log                hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS S3 server access logs capture detailed information about the requests that are made to a bucket. This table provides a structured representation of the log data.
aws_vpc_flow_log                        hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS VPC Flow Logs capture information about IP traffic going to and from network interfaces in your VPC. This table provides detailed network traffic patterns, including source and destination IP addresses, ports, protocols, and traffic volumes, helping teams monitor network flows, troubleshoot connectivity issues, and detect security anomalies.
aws_waf_traffic_log                     hub.tailpipe.io/plugins/turbot/aws@latest    -             -        -          AWS WAF traffic logs record detailed web request data, helping analyze threats, monitor rule effectiveness, and improve security posture.

```


## Custom Tables
Custom tables are a means to collect data from arbitrary log files and other sources.  This is useful if you need to analyze logs that have custom or proprietary formats, or to collect logs that do not have plugin support.

Supported data formats are:, 
- Delimited (CSV, TSV)
- JSONL
- Text files with any line format for which a regex pattern can be defined 

Custom tables are defined in a `table` block. Table blocks have a single label, which is the name of the table. They define the schema and processing rules for your data, specifying how to parse and transform your source data into a queryable format.

The format of the source data is defined by the `format` property, which must reference either a format block defined in the config or a format preset defined by a plugin.

To create a custom table, define a `table` and then create one or more partitions for the table.

```hcl
table "openstack_syslog" {
  format     = format.regex.openstack_syslog
  null_value = "-"

  column "tp_timestamp"{
    source = "timestamp"
  }

  column "tp_index" {
    source = "tenant_id"
  }
  column "tenant_id" {
    source = "tenant_id"
    type = "uuid"
  }
}

format "regex" "openstack_syslog"{
  layout = `^(?P<log_file>nova-[\w-]+\.log(?:\.\d+)?\.[\d-]+_[\d:]+)\s+(?P<timestamp>[\d-]+\s+[\d:.]+)\s+(?P<pid>\d+)\s+(?P<log_level>\w+)\s+(?P<component>[\w._-]+)(?:\s+(?:\[(?:req-(?P<request_id>[^\s]+)\s+(?P<user_id>[^\s]+)\s+(?P<tenant_id>[^\s]+)(?:\s+[^\]]+)?|-)]\s+)?(?:(?P<client_ip>\d+\.\d+\.\d+\.\d+)\s+"(?P<http_method>GET|POST|PUT|DELETE)\s+(?P<http_path>[^\s]+)\s+HTTP\/[\d.]+"\s+status:\s+(?P<http_status>\d+)\s+len:\s+(?P<resp_size>\d+)\s+time:\s+(?P<resp_time>[\d.]+)|(?:\[instance:\s+(?P<instance_id>[^\]]+)\])?\s*(?P<message>.*))?)?$`
}

partition "openstack_syslog" "midterms" {
  source "file"  {
    paths       = ["/Downloads/OpenStack"]
    file_layout = ".log$"
  }
}

```


## Common Fields

Tailpipe plugins populate a set of common fields. Some are mandatory, for example `tp_partition` and `tp_date`. Others, like `tp_source_ip` and `tp_ips`, are optional. Plugins map table-specific fields to these common fields when it is appropriate to do so. The AWS Cloudtrail plugin, for example, maps the value of the native field `SourceIPAddress` to the common field `tp_source_ip`. It also adds that address to the `tp_ips` array.

These mappings enable queries that correlate values across different logs. If you have collected both Cloudtrail and ALB logs, for example, you could query for source addresses that occur in both the `aws_cloudtrail_log` and `aws_alb_access_log` tables.

| Field Name | Type |
|------------|------|
| tp_akas | varchar[] |
| tp_date | date |
| tp_destination_ip | varchar |
| tp_domains | varchar[] |
| tp_emails | varchar[] |
| tp_id | varchar |
| tp_index | varchar |
| tp_ingest_timestamp | timestamp |
| tp_ips | varchar[] |
| tp_partition | varchar |
| tp_source_ip | varchar |
| tp_source_location | varchar |
| tp_source_name | varchar |
| tp_source_type | varchar |
| tp_table | varchar |
| tp_tags | varchar[] |
| tp_timestamp | timestamp |
| tp_usernames | varchar[] |
