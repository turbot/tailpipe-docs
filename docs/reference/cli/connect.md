---
title: tailpipe connect
---

# tailpipe connect

Return a connection string for a database, with a schema determined by the provided parameters.

# Usage
```bash
 tailpipe connect [flags]
 ```

>[!NOTE]
> Powerpipe does this automatically. I could use it manually to avoid conflict, if querying with DuckDB (or Tailpipe), is there another purpose?


### Arguments

| Flag | Description
|-|-
| `--from string`    |  Specify the start time
| `--to string`      |  Specify the end time
| `--output output`  |  Output format; one of: json, table (default text)
|  `--help`          |  Help for connect

## Examples

Connect to the Tailpipe database, filtered to records after the start time:

```bash
tailpipe connect --from 15/11/2024
```

Show the filepath for the connected database:

```bash
tailpipe connect --output json
```

```json
{ 'database_filepath":"/home/jon/.tailpipe/data/default/tailpipe_20241212134120.db"}
```

Use Tailpipe to query the connected database:

>[!NOTE]
> Not a `tailpipe query` option, yet, will it be?

Use DuckDB to query the connected database:

```bash
duckdb /home/jon/.tailpipe/data/default/tailpipe_20241212134120.db
```