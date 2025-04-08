---
title: Manage Your Data

---

***TO DO***

view tables, partitions, e tc

delete partitions

compact data

`tailpipe connect`



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