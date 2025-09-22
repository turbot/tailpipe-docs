---
title: tailpipe connect
---

# tailpipe connect

Return the path of SQL script to initialise DuckDB to use the tailpipe database.

The generated SQL script contains:
- DuckDB extension installations (sqlite, ducklake)
- Database attachment configuration
- View definitions with optional filters

## Usage
```bash
 tailpipe connect [flags]
 ```

### Arguments

| Flag | Description
|-|-
| `--from string`    |  Specify the start time
| `--help`           |  Help for connect
| `--index strings`  |  Specify the index(es) to use
| `--output`         |  Output format: `json`, `plain`, `pretty` (default)
| `--partition strings`  |  Specify the partition(s) to use
| `--to string`      |  Specify the end time


## Examples

Connect to the Tailpipe database, filtered to records after the start time:

```bash
tailpipe connect --from 2025-01-01
```

```bash
/Users/pskrbasu/.tailpipe/data/default/tailpipe_init_20250918204456.sql
```

> [!NOTE]
> You can use this sql script with DuckDB to directly query the Tailpipe database.
To ensure compatibility with DuckLake features, make sure you’re using DuckDB version 1.4.0 or later.
> 
> ```bash
>  duckdb -init /Users/pskrbasu/.tailpipe/data/default/tailpipe_init_20250918204456.sql
> ```

Connect with no filter, show output as json:

```bash
tailpipe connect --output json
```

```bash
{"init_script_path":"/Users/pskrbasu/.tailpipe/data/default/tailpipe_init_20250918204828.sql"}
```

