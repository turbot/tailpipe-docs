---
title: Coding Standards
sidebar_label: Coding Standards
---

# Coding Standards

## Code Formatting

Code should be formatted with <a href="https://golang.org/cmd/gofmt/" target="_blank" rel="noopener noreferrer">gofmt</a>.

## Repo Structure

Each plugin should reside in a separate GitHub repository named `tailpipe-plugin-{plugin name}`. The repo should contain:

- `README.md`
- `LICENSE`
- `Makefile`
- `main.go` - along with corresponding `go.mod` and `go.sum` files.
- `CHANGELOG.md` - for tracking version changes.

Along with the following directories
- `{plugin name}` (required): This should contain `plugin.go` and may include `plugin_test.go`.
- `config` (required): Contains connection configuration files specific to the plugin (e.g., `{plugin_name}_connection.go`).
- `tables` (required): Contains subdirectories for each table, where each subdirectory includes all files related to that table:
  - `{model_name}/{model_name}.go` - Defines the table schema (row definition)
  - `{model_name}/{model_name}_table.go` - Contains the table definition
  - `{model_name}/{model_name}_mapper.go` - Contains any mappers required by the table (if needed)
  - `{model_name}/{model_name}_extractor.go` - Contains extractors for the table (if needed)
  - If a table requires a custom format (like the `custom_log` example in the repo structure), include:
    - `{model_name}/{model_name}_table_format.go` - Format handling for the custom log
    - `{model_name}/{model_name}_table_format_presets.go` - Preset formats for the custom log
- `sources` (optional): Contains subdirectories for each source type, where each subdirectory includes:
  - `{source_name}/{source_name}_source.go` - Contains the source implementation
  - `{source_name}/{source_name}_source_config.go` - Contains the source configuration
- `docs` (required): Containing documentation, especially table docs:
  - `docs/index.md` - Main plugin documentation
  - `docs/tables/{table_name}/index.md` - Main documentation for each table
  - `docs/tables/{table_name}/queries.md` - Example queries for each table

### Example: Repo Structure

```bash
tailpipe-plugin-example
├── CHANGELOG.md
├── LICENSE
├── Makefile
├── README.md
├── config
│   └── example_connection.go
├── docs
│   ├── index.md
│   └── tables
│       ├── access_log
│       │   ├── index.md
│       │   └── queries.md
│       ├── access_log
│       │   ├── index.md
│       │   └── queries.md
│       └── custom_log
│           ├── index.md
│           └── queries.md
├── example
│   ├── plugin.go
│   └── plugin_test.go
├── go.mod
├── go.sum
├── main.go
├── sources
│   └── audit_log_api
│       ├── audit_log_api_source.go
│       └── audit_log_api_source_config.go
└── tables
    ├── access_log
    │   ├── access_log.go
    │   ├── access_log_extractor.go
    │   └── access_log_table.go
    ├── audit_log
    │   ├── audit_log.go
    │   ├── audit_log_mapper.go
    │   └── audit_log_table.go
    └── custom_log
        ├── custom_log_table.go
        ├── custom_log_table_format.go
        └── custom_log_table_format_presets.go
```

## File Naming Conventions

When creating files for your plugin, follow these naming conventions:

- **Plugin Files**: `plugin.go` and optionally `plugin_test.go` in the plugin-specific directory.
- **Connection Files**: `{plugin_name}_connection.go` in the `config` directory.
- **Table Files**: 
  - `{model_name}.go` - Row definition file
  - `{model_name}_table.go` - Table definition file
  - `{model_name}_mapper.go` - Mapper file (if needed)
  - `{model_name}_extractor.go` - Extractor file (if needed)
    - `{model_name}_table_format.go` - Format handling for the custom log (if needed)
    - `{model_name}_table_format_presets.go` - Preset formats for the custom log (if needed)
- **Source Files**:
  - `{source_name}_source.go` - Source implementation
  - `{source_name}_source_config.go` - Source configuration

## Optional Directories

In addition to the required directories, plugins may include the following optional directories:

- **scripts**: Contains utility scripts for development, testing, or deployment.
- **tests**: Contains test files and test data for the plugin.

These directories should follow the same naming conventions and organization principles as the rest of the repository.
