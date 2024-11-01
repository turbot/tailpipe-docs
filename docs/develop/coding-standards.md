---
title: Coding Standards
sidebar_label: Coding Standards
---

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
- `models` (required): Contains go files for models (table row schema) used by the plugin.
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
│   └── plugin.go
├── go.mod
├── go.sum
├── main.go
├── docs
│   ├── index.md
│   └── tables
│       ├── access_log.md
│       └── audit_log.md
├── mappers
│   ├── access_log_mapper.go
│   └── audit_log_mapper.go
├── models
│   ├── access_log.go
│   └── audit_log.go
├── sources
│   ├── audit_log_api_source.go
│   └── audit_log_api_source_config.go
└── tables
    ├── access_log_table.go
    ├── access_log_table_config.go
    ├── audit_log_table.go
    └── audit_log_table_config.go
```
