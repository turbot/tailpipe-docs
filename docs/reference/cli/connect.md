---
title: tailpipe connect
---

# tailpipe connect

Return a connection string for a database, with a schema determined by the provided parameters.

# Usage
```bash
 tailpipe connect [flags]
 ```

### Arguments

| Flag | Description
|-|-
| `--from string`    |  Specify the start time
|  `--help`          |  Help for connect
| `--index strings`      |  Specify the index(es) to use
|  `--output`          |  Output format; one of: json, table (default text)
| `--partition strings`  |  Specify the partition(s) to use
| `--to string`      |  Specify the end time

## Examples

Connect to the Tailpipe database, filtered to records after the start time:

```bash
tailpipe connect --from 2025-01-01
/home/jon/.tailpipe/data/default/tailpipe_20250115140447.db
```

> [!NOTE]
> You could use this connection string with DuckDB:
> 
> duckdb /home/jon/.tailpipe/data/default/tailpipe_20241212134120.db

```bash
tailpipe connect --from 2025-01-01
/home/jon/.tailpipe/data/default/tailpipe_20250115140447.db
```

```bash
tailpipe connect --from 2025-01-01
/home/jon/.tailpipe/data/default/tailpipe_20250115140447.db
```

Show the filepath for the connected database:

```bash
tailpipe connect --output json
```

