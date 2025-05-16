---
title: Coding Standards
sidebar_label: Coding Standards
---

# Coding Standards

- [Code Formatting](#code-formatting)
- [Repo Structure](#repo-structure)
- [File Naming Conventions](#file-naming-conventions)
- [Optional Directories](#optional-directories)

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
- `sources` (optional): Contains subdirectories for each source type, where each subdirectory includes:
  - `{source_name}/{source_name}_source.go` - Contains the source implementation
  - `{source_name}/{source_name}_source_config.go` - Contains the source configuration
- `docs` (required): Containing documentation, especially table docs.

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
│       ├── access_log.md
│       └── audit_log.md
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
    └── audit_log
        ├── audit_log.go
        ├── audit_log_mapper.go
        └── audit_log_table.go
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
- **Source Files**:
  - `{source_name}_source.go` - Source implementation
  - `{source_name}_source_config.go` - Source configuration

## Optional Directories

In addition to the required directories, plugins may include the following optional directories:

- **scripts**: Contains utility scripts for development, testing, or deployment.
- **tests**: Contains test files and test data for the plugin.

These directories should follow the same naming conventions and organization principles as the rest of the repository.
