---
title: Manage Your Data

---


# Manage Your Data

The Tailpipe CLI makes it easy to find information about the data that you have collected.


## Viewing Table Information

You can see which tables are available with the `tailpipe table list` command.  

```bash
tailpipe table list
```

The output will include any table defined in plugins that you have installed (even if you have not collected any data for them) as well as any [custom tables](#custom-tables) you have defined.  For each table, you can see the plugin that implements it, as well as a description.  If you have collected data for the table, you will see the number of parquet files, rows, and total size.

```bash
$ tailpipe table list
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

If you prefer, you can get the output in JSON format:
```bash
tailpipe table list --output json
```

To view details of a single table, use the `show` command:
```bash
tailpipe table show aws_cloudtrail_log
```

The output includes the name and description, a list of all columns in the table, the file count and size, and a list of partitions that comprise the table.

```
Name:        aws_cloudtrail_log
Description: AWS CloudTrail logs capture API activity and user actions within your AWS account.
Columns:     
  additional_event_data: json
  api_version: varchar
  aws_region: varchar
  edge_device_details: json
  error_code: varchar
  error_message: varchar
  event_category: varchar
  event_id: varchar
  event_name: varchar
  event_source: varchar
  event_time: timestamp
  event_type: varchar
  event_version: varchar
  management_event: boolean
  read_only: boolean
  recipient_account_id: varchar
  request_id: varchar
  request_parameters: json
  resources: json
  response_elements: json
  service_event_details: json
  session_credential_from_console: varchar
  shared_event_id: varchar
  source_ip_address: varchar
  tls_details: struct
  user_agent: varchar
  user_identity: struct
  vpc_endpoint_id: varchar
  tp_akas: varchar[]
  tp_date: date
  tp_destination_ip: varchar
  tp_domains: varchar[]
  tp_emails: varchar[]
  tp_id: varchar
  tp_index: varchar
  tp_ingest_timestamp: timestamp
  tp_ips: varchar[]
  tp_partition: varchar
  tp_source_ip: varchar
  tp_source_location: varchar
  tp_source_name: varchar
  tp_source_type: varchar
  tp_table: varchar
  tp_tags: varchar[]
  tp_timestamp: timestamp
  tp_usernames: varchar[]
Status:      
  local file count: 8
  local file size: 23 MB
Partitions:  
  prod

```

## Viewing Partitions

You can list all the partitions you have collected:
```bash
tailpipe partition list
```

The list command will list all the partitions, including the name, the plugin name, the total size, the number of files, and the row count.
```
NAME                       PLUGIN                                       LOCAL SIZE    FILES    ROWS
aws_cloudtrail_log.prod    hub.tailpipe.io/plugins/turbot/aws@latest    23 MB         8        100,397
steampipe_plugin.local     core                                         359 kB        1        15,386
syslog.local               core                                         -             -        -
```

If you prefer, you can get the results as JSON:
```bash
tailpipe partition list --output json
```

You can view details of a single partition with the `show` command:
```bash
tailpipe partition show aws_cloudtrail_log.prod 
```
```
Name:        aws_cloudtrail_log.prod
Plugin:      hub.tailpipe.io/plugins/turbot/aws@latest
Local Size:  23 MB
Local Files: 8 B
```


## Deleting Data

You can delete collected data with the `tailpipe partition delete` command.

```bash
tailpipe partition delete aws_cloudtrail_log.prod
```

By default, you will be prompted to confirm before deleting:
```bash
$ tailpipe partition delete aws_cloudtrail_log.prod
Are you sure you want to delete partition aws_cloudtrail_log.prod? (y): y

Deleted partition 'aws_cloudtrail_log.prod' (deleted 8 parquet files).
```

You can use the `--force` flag to skip the confirmation:
```bash
tailpipe partition force 
```


<!--  I guess we didn't implement this yet???
You can delete *all partitions* for a table by using  the `*` wildcard for the partition name:
```bash
tailpipe partition delete 'aws_cloudtrail_log.*'
```

or by specifying only the table name:
```bash
tailpipe partition delete aws_cloudtrail_log
```

You can wildcard the table name to delete all partitions with the given name in all tables:
```bash
tailpipe partition delete '*.prod'
```

Or even delete all partitions in all tables:
```bash
tailpipe partition delete '*.*'
```
-->


You can delete all data for the partition after a specific date:
```bash
tailpipe partition delete aws_cloudtrail_log.prod --from=2025-04-01
```

Or use relative dates in the `--from` argument:
```bash
tailpipe partition delete aws_cloudtrail_log.prod --from=T-2Y   # (2 years ago)
tailpipe partition delete aws_cloudtrail_log.prod --from=T-10m  # (10 months ago)
tailpipe partition delete aws_cloudtrail_log.prod --from=T-10W  # (10 weeks ago)
tailpipe partition delete aws_cloudtrail_log.prod --from=T-180d # (180 days ago)
tailpipe partition delete aws_cloudtrail_log.prod --from=T-9H   # (9 hours ago)
tailpipe partition delete aws_cloudtrail_log.prod --from=T-10M  # (10 minutes ago)
```


## Compacting Files

By default, Tailpipe compacts files when you run `tailpipe collect`.  You can disable this if you want to:

```bash
tailpipe collect --compact=false
```

You can then manually compact files with the `tailpipe compact` command.  You can compact everything:
```bash
tailpipe compact
```

or a single table:
```bash
tailpipe compact aws_cloudtrail_log
```

or a single partition:
```bash
tailpipe compact aws_cloudtrail_log.prod
```

## Viewing Formats

[Formats](/docs/reference/config-files/format) describe the layout of the source data so that it can be collected into a table, and each format has a type.  [Formats types](/docs/reference/config-files/format#format-types) are implemented by plugins and define the parsing mechanism which should be used.  Plugins may also export preset formats that you can use.

You can list the available formats and format types in your installation:
```bash
tailpipe format list
```
```
TYPE                NAME                LOCATION                                        DESCRIPTION
delimited           -                   hub.tailpipe.io/plugins/turbot/core@latest      This is a format type, it can be used for defining instances of formats.
delimited           default             hub.tailpipe.io/plugins/turbot/core@latest      Default Delimited format
grok                -                   hub.tailpipe.io/plugins/turbot/core@latest      This is a format type, it can be used for defining instances of formats.
grok                custom_log          /Users/jsmyth/.tailpipe/config/aws.tpc          
grok                steampipe_plugin    /Users/jsmyth/.tailpipe/config/steampipe.tpc    
jsonl               -                   hub.tailpipe.io/plugins/turbot/core@latest      This is a format type, it can be used for defining instances of formats.
jsonl               default             hub.tailpipe.io/plugins/turbot/core@latest      Default JSONL format
nginx_access_log    -                   hub.tailpipe.io/plugins/turbot/nginx@latest     This is a format type, it can be used for defining instances of formats.
nginx_access_log    combined            hub.tailpipe.io/plugins/turbot/nginx@latest     Predefined Nginx combined log format.
regex               -                   hub.tailpipe.io/plugins/turbot/core@latest      This is a format type, it can be used for defining instances of formats.
```

You can also view the details of a single format type:
```bash
tailpipe format show nginx_access_log
```
```
Type:        nginx_access_log
Location:    hub.tailpipe.io/plugins/turbot/nginx@latest
Description: This is a format type, it can be used for defining instances of formats.
```


or format:
```bash
tailpipe format show nginx_access_log.combined
```
```
Type:        nginx_access_log
Name:        combined
Location:    hub.tailpipe.io/plugins/turbot/nginx@latest
Description: Predefined Nginx combined log format.
Regex:       ^(?P<remote_addr>[^ ]*) - (?P<remote_user>[^ ]*) \[(?P<time_local>[^\]]*)\] "(?P<request_method>\S+)(?: +(?P<request_uri>[^ ]+))?(?: +(?P<server_protocol>\S+))?" (?P<status>[^ ]*) (?P<body_bytes_sent>[^ ]*) "(?P<http_referer>.*?)" "(?P<http_user_agent>.*?)"
Layout:      $remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent"
```

## Viewing Source Types

A partition acquires data from a [source](/docs/reference/config-files/partition#source), each of which has a source type.  The source types are defined in plugins, and you can view them with the `tailpipe source list` command:

```bash
tailpipe source list
```
```
NAME             PLUGIN                                        DESCRIPTION
aws_s3_bucket    hub.tailpipe.io/plugins/turbot/aws@latest     
file             hub.tailpipe.io/plugins/turbot/core@latest   
```

You can also view the details of a single source type:
```bash
tailpipe source show aws_s3_bucket
```
```
Name:        aws_s3_bucket
Plugin:      hub.tailpipe.io/plugins/turbot/aws@latest
```


## Connecting from Other Tools
You can connect to your Tailpipe database with the native DuckDB client or other tools and libraries that can connect to DuckDB.  To do so, you can generate a new db file for the connection using `tailpipe connect`:

```bash
tailpipe connect
```

A new DB file will be generated and returned:
```bash
$ tailpipe connect
/Users/jsmyth/.tailpipe/data/default/tailpipe_20250409151453.db
```

If you've collected a lot of data and want to optimize your queries for a subset of it, you can pre-filter the database. You can restrict to the most recent 45 days:

```bash
tailpipe connect --from T-45d
```

Or to a range:

```bash
tailpipe connect --from 2024-12-01 --to 2025-01-01
```

Or to a specific index:
```bash
tailpipe connect --index 123456789
```

Or a partition:
```bash
tailpipe connect --partition aws_cloudtrail_log.prod
```

You can include multiple filters:
```bash
tailpipe connect --partition aws_cloudtrail_log.prod --index 123456789 --from 2024-12-01 --to 2025-01-01
```