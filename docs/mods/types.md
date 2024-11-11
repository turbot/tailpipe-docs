---
title: Mod Types
sidebar_label: Mod Types
---

# Mod Types

## Detection Mods

- Each plugin should have a detection mod, e.g., AWS Detections, Azure Detections

### Detections

Standards for fields:

- `title`
- `description`
- `severity`
- `references`

The detection should have the following tags:

```hcl
category: "Detection"
plugin: "<plugin short name>", e.g., "aws", "azure", "github"
service: "<plugin display name>/<log source service>", e.g., "AWS/CloudTrail", "Azure/Monitor"
type: "detection"
```

Detection queries should have the following columns in this order:

- `timestamp`
- `operation`
- `resource`
- `actor`
- `source_ip`
- `tp_index`, use an alias, e.g., `account_id`, `subscription_id`
- `<additional account context>`, e.g., `region`, `resource_group`
- `source_id`

The query should also have `*`, which is generally discouraged, but helpful in these queries to make all row data available.

### Detection Benchmarks

Contains all detections for a given log type (usually per table).

The benchmark should have the following tags:

```hcl
category: "Detection"
plugin: "<plugin short name>", e.g., "aws", "azure", "github"
service: "<plugin display name>/<log source service>", e.g., "AWS/CloudTrail", "Azure/Monitor"
type: "benchmark"
```

### Framework Benchmarks

Contains sub-benchmarks that map to common framework, e.g., [MITRE ATT&CK](https://attack.mitre.org/).

The benchmark should have the following tags:

```hcl
category: "Detection"
plugin: "<plugin short name>", e.g., "aws", "azure", "github"
service: "<plugin display name>", e.g., "AWS", "Azure"
type: "benchmark"
```

## Insights Mods

## Billing Mods

