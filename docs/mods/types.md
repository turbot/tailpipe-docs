---
title: Mod Types
sidebar_label: Mod Types
---

# Mod Types

## Detection Mods

- Each plugin should have a detection mod, e.g., AWS Detections, Azure Detections

### Structure

Each mod contains benchmarks and detections:

- Individual detections
  - Detections that run queries to check for anomalies
  - Standards for fields:
    - `title`
    - `description`
    - `severity`
    - `query`
      - Detection queries should have the following columns in this order:
        - `timestamp`,
        - `operation`,
        - `resource`,
        - `actor`,
        - `source_ip`,
        - `tp_index`, use an alias, e.g., `account_id`, `subscription_id`
        - `<additional account context>`, e.g., `region`, `resource_group`
        - `source_id`
      - The query should also have `*`, which is generally discouraged in queries, to make all row data available
    - `references`
    - `tags`
      - category: `Detection`
      - plugin: `<plugin short name>`, e.g., `aws`, `azure`, `github`
      - service: `<plugin display name>/<log source service>`, e.g., `AWS/CloudTrail`, `Azure/Monitor`
      - type: `detection`
- Detection benchmarks
  - Contains all detections for a given log table/log type
  - Tags:
    - category: `Detection`
    - plugin: `<plugin short name>`, e.g., `aws`, `azure`, `github`
    - service: `<plugin display name>/<log source service>`, e.g., `AWS/CloudTrail`, `Azure/Monitor`
    - type: `benchmark`
- Framework benchmarks
  - Contains sub-benchmarks that map to common frame, e.g., [MITRE ATT&CK](https://attack.mitre.org/)
    - category: `Detection`
    - plugin: `<plugin short name>`, e.g., `aws`, `azure`, `github`
    - service: `<plugin display name>`, e.g., `AWS`, `Azure`
    - type: `benchmark`

## Insights Mods

## Billing Mods

Access
Findings
Network flow logs
Kube?
# Coding Standards

- [Code Formatting](#code-formatting)
- [Comments](#comments)
- [Repo Structure](#repo-structure)

## Code Formatting

Code should be formatted with <a href="https://golang.org/cmd/gofmt/" target="_blank" rel="noopener noreferrer">gofmt</a>.

## Comments

TBD.

## Repo Structure

Each plugin should reside in a separate GitHub repository, named `tailpipe-plugin-{plugin name}`. The repo should contain:

- `README.md`
- `LICENSE`
- `Makefile`
- `main.go` - along with corresponding `go.mod` and `go.sum` files.

Along with the following directories
- `{plugin name}` (required): This should contain `plugin.go`.
- `mappers` (optional): May contain go files for any specific mappers required by the plugin in format `{model_name}_mapper.go`.
- `rows` (required): Contains go files which define the table schema(s) used in the plugin.
- `sources` (optional): May contain go files for any sources required that aren't provided by the SDK, along with any required configuration schemas.
- `tables` (required): Contains go files for table definitions `{model_name}_table.go` and table specific configuration `{model_name}_table_config.go`.
- `docs` (required): Containing documentation, especially table docs.

### Example: Repo Structure

```sh
tailpipe-plugin-example
├── LICENSE
├── Makefile
├── README.md
├── example
│   └── plugin.go
├── go.mod
├── go.sum
├── main.go
├── docs
│   ├── index.md
│   └── tables
│       ├── access_log.md
│       └── audit_log.md
├── mappers
│   ├── access_log_mapper.go
│   └── audit_log_mapper.go
├── rows
│   ├── access_log.go
│   └── audit_log.go
├── sources
│   ├── audit_log_api_source.go
│   └── audit_log_api_source_config.go
└── tables
    ├── access_log_table.go
    ├── access_log_table_config.go
    ├── audit_log_table.go
    └── audit_log_table_config.go
```
