---
title: Mod Types
sidebar_label: Mod Types
---

# Mod Types

## Detection Mods

Each plugin should have a detection mod, e.g., AWS Detections, Azure Detections.

### Detections

Standards for fields:

- title: `Detect <detection> in <log types>`
- description: `Detect <detection> to check <impact>.`
- severity: Select a severity from `critical`, `high`, `medium`, or `low`.

The detection should have the following tags:

- category: `Detection`
- plugin: `<plugin short name>`, e.g., `aws`, `azure`, `github`
- service: `<plugin display name>/<log source service>`, e.g., `AWS/CloudTrail`, `Azure/Monitor`
- type: `detection`
- mitre_attack_ids: Comma separated string of MITRE ATT&CK IDs, e.g., ``TA0001:T1190,TA0005:T1562``

Detection queries should have the following columns in this order:

- `timestamp` - Usually set as `tp_timestamp`
- `operation` - Operation name, e.g., `ec2:TerminateInstances`, `packages.package_version_published`
- `resource` - Primary resource affected by the event
- `actor` - Primary actor that triggered the event
- `source_ip` - Usually set as `tp_source_ip` if available
- `tp_index` with an alias, e.g., `tp_index as account_id`, `tp_index as subscription_id`
- `<additional account context>`, e.g., `region`, `resource_group`
- `source_id` - Usually set as `tp_id`

The query should also have `*`, which is generally discouraged, but helpful in these queries to make all row data available.

Example detection:

```hcl
detection "cloudtrail_logs_cloudtrail_trail_updates" {
  title       = "Detect CloudTrail Trail Updates in CloudTrail Logs"
  description = "Detect CloudTrail trail changes to check if logging was stopped."
  severity    = "medium"
  query       = query.cloudtrail_logs_cloudtrail_trail_updates

  tags = merge(local.cloudtrail_logs_common_tags, {
    mitre_attack_ids = "TA0005:T1562:001"
  })
}
```

### Detection Benchmarks

Contains all detections for a given log type (usually per table).

Standards for fields:

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

