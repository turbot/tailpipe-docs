---
title: Mod Types
sidebar_label: Mod Types
---

# Mod Types

## Detection Mods

Each plugin should have a detection mod, e.g., AWS Detections, Azure Detections.

### Detections

Standards for names/fields:

- name: `<log types>_detect_<detection>`
- title: `Detect <detection>`
- description: `Detect <detection> to check <impact>.`
- severity: Select a severity from `critical`, `high`, `medium`, or `low`.

The detection should have the following tags:

- category: `Detection`
- plugin: `<plugin short name>`, e.g., `aws`, `azure`, `github`
- service: `<plugin display name>/<log source service>`, e.g., `AWS/CloudTrail`, `Azure/Monitor`
- type: `detection`
- mitre_attack_ids: Comma separated string of MITRE ATT&CK IDs, e.g., ``TA0001:T1190,TA0005:T1562``

Example detection:

```hcl
detection "cloudtrail_logs_detect_cloudtrail_trail_updates" {
  title       = "Detect CloudTrail Trail Updates"
  description = "Detect CloudTrail trail changes to check if logging was stopped."
  severity    = "medium"
  query       = query.cloudtrail_logs_cloudtrail_trail_updates

  tags = merge(local.cloudtrail_log_detections_common_tags, {
    mitre_attack_ids = "TA0005:T1562:001"
  })
}
```

### Detection Queries

Standards for names/fields:

- name: `<log types>_detect_<detection>`

Other notes:

- Detection queries should not return rows that resulted in errors or failed
- Detection queries should order by `timestamp` in descending order to show the most recent logs first

Detection queries should have the following columns in this order:

- `timestamp` - Usually set as `tp_timestamp`
- `operation` - Operation name, e.g., `ec2:TerminateInstances`, `packages.package_version_published`
- `resource` - Primary resource affected by the event
- `actor` - Primary actor that triggered the event
- `source_ip` - Usually set as `tp_source_ip` if available
- `tp_index` with an alias, e.g., `tp_index as account_id`, `tp_index as subscription_id`
- `<additional account context>`, e.g., `region`, `resource_group`
- `source_id` - Usually set as `tp_id`
- `*` - This is generally discouraged, but helpful to make all row data available

To make mod writing easier, we also recommend using HCL locals to set query columns. For instance:

```hcl
locals {
  # Local internal variables to build the SQL select clause for common
  # dimensions. Do not edit directly.
  cloudtrail_log_detection_sql_columns = <<-EOQ
  tp_timestamp as timestamp,
  string_split(event_source, '.')[1] || ':' || event_name as operation,
  __RESOURCE_SQL__ as resource,
  user_identity.arn as actor,
  tp_source_ip as source_ip,
  tp_index::varchar as account_id,
  aws_region as region,
  tp_id as source_id,
  *
  EOQ
}

locals {
  cloudtrail_logs_detect_cloudtrail_trail_updates_sql_columns = replace(local.cloudtrail_log_detection_sql_columns, "__RESOURCE_SQL__", "request_parameters::JSON ->> 'name'")
}

query "cloudtrail_logs_detect_cloudtrail_trail_updates" {
  sql = <<-EOQ
    select
      ${local.cloudtrail_logs_detect_cloudtrail_trail_updates_sql_columns}
    from
      aws_cloudtrail_log
    where
      event_source = 'cloudtrail.amazonaws.com'
      and event_name in ('DeleteTrail', 'StopLogging', 'UpdateTrail')
      and error_code is null
    order by
      event_time desc;
  EOQ
  }
```

If the `resource` column is static:

```hcl
locals {
  s3_server_access_log_detection_sql_columns = <<-EOQ
  tp_timestamp as timestamp,
  operation as operation,
  bucket as resource,
  requester as actor,
  tp_source_ip as source_ip,
  tp_index::varchar as account_id,
  'us-east-1' as region, -- TODO: Use tp_location when available
  tp_id as source_id,
  *
  EOQ
}

query "s3_server_access_logs_detect_insecure_access" {
  sql = <<-EOQ
    select
      ${local.s3_server_access_log_detection_sql_columns}
    from
      aws_s3_server_access_log
    where
      operation ilike 'REST.%.OBJECT' -- Ignore S3 initiated events
      and (cipher_suite is null or tls_version is null)
    order by
      timestamp desc
  EOQ
}
```

### Detection Benchmarks

Contains all detections for a given log type (usually per table).

Standards for fields:

- name: `<log type>_detection`
- title: `<Log type> Detections`
- description: `This benchmark contains recommendations when scanning <log types>.`
- type: `detection`
- children: A list of children benchmarks and detections.

The benchmark should have the following tags:

- category: `Detection`
- plugin: `<plugin short name>`, e.g., `aws`, `azure`, `github`
- service: `<plugin display name>/<log source service>`, e.g., `AWS/CloudTrail`, `Azure/Monitor`
- type: `benchmark`

Example benchmark:

```hcl
detection_benchmark "cloudtrail_log_detections" {
  title       = "CloudTrail Log Detections"
  description = "This detection_benchmark contains recommendations when scanning CloudTrail logs."
  type        = "detection"
  children = [
    detection.cloudtrail_logs_cloudtrail_trail_updates,
    detection.cloudtrail_logs_ec2_security_group_ingress_egress_updates,
    detection.cloudtrail_logs_iam_entity_created_without_cloudformation,
    detection.cloudtrail_logs_iam_root_console_logins,
    detection.cloudtrail_logs_iam_user_login_profile_updates,
  ]

  tags = merge(local.cloudtrail_logs_common_tags, {
    type = "Benchmark"
  })
}
```

### Framework Benchmarks

Contains benchmarks that map to common framework, e.g., [MITRE ATT&CK](https://attack.mitre.org/).

Each benchmark for a particular framework benchmark should have the following tags:

- category: `Detection`
- plugin: `<plugin short name>`, e.g., `aws`, `azure`, `github`
- service: `<plugin display name>`, e.g., `AWS`, `Azure`
- type: `benchmark`

## Insights Mods

## Billing Mods

