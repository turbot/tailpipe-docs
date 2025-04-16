---
title: tailpipe table
---

# tailpipe table

List and show Tailpipe tables.

## Usage
```bash
tailpipe table_name list [args]
tailpipe table_name show [args]
```

## Sub-Commands

| Command | Description
|-|-
| [list](#tailpipe-table-list) | List all tables.
| [show](#tailpipe-table-show)  | Show details of a table.


## tailpipe table list
List all tables.

### Arguments

| Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    | Output format: `json`, `plain`, `pretty` (default) 

### Examples

List tables.

```bash
tailpipe table list
```

List all tables in `JSON` format. 

```bash
tailpipe table list --output json
```

## tailpipe table show
Show details for a table.

### Arguments

| Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    | Output format: `json`, `plain`, `pretty` (default) 

### Examples

Show details in table format.

```bash
tailpipe table show pipes_audit_log
```

Show details in JSON format.

```bash
tailpipe table show pipes_audit_log --output json
```
