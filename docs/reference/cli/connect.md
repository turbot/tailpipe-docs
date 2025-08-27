---
title: tailpipe connect
---

# tailpipe connect

Return a connection string for a database with a schema determined by the provided parameters.

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
/home/jon/.tailpipe/data/default/tailpipe_20250115140447.db
```

> [!NOTE]
> You can use this connection string with DuckDB to directly query the Tailpipe database.
To ensure compatibility with tables that include JSON columns, make sure you’re using DuckDB version 1.1.3 or later.
> 
> ```bash
> duckdb /home/jon/.tailpipe/data/default/tailpipe_20241212134120.db
> ```

Connect with no filter, show output as json:

```bash
tailpipe connect --output json
```

```bash
{"database_filepath":"/Users/jonudell/.tailpipe/data/default/tailpipe_20250129204416.db"}
```

