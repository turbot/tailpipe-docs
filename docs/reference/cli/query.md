---
title: tailpipe query
---

# tailpipe query

Query a Tailpipe table

## Usage
```bash
tailpipe query [sql] [flags]
```

Execute SQL queries interactively, or by a query argument.

To open the interactive query shell, run `tailpipe query` with no arguments.  The query shell provides a way to explore your data and run multiple queries. 

If a query string is passed on the command line then it will be run immediately and the command will exit.  Alternatively, you may specify one or more files containing SQL statements.  You can run multiple SQL files by passing a glob or a space-separated list of file names.

## Usage
Run Tailpipe [interactive query shell](/docs/query/query-shell):
```bash
tailpipe query [flags]
```

Run a [batch query](/docs/query/batch-query):
```bash
tailpipe query {query} [flags]
```


## Flags

| Argument  | Description  
|--|--
| `--from string`                | Specify the start time
| `--header string`              | Specify whether to include column headers in csv and table output (default true`)|
| `--help`                       | Help for `tailpipe query`
| `--index strings`              | Specify the index(es) to use
| `--output output`               | One of: table, csv, json, line (default table)
| `--partition strings`          | Specify the partition(s) to use
| `--separator string`          |  Separator string for csv output (default ",")
| `--to string`                | Specify the end time

### Global Flags

<table>
  <tr> 
    <th> Flag </th> 
    <th> Description </th> 
  </tr>

  <tr> 
    <td nowrap="true"> `--config-path`</td> 
    <td>  
    Sets the search path for <a href = "/docs/reference/config-files">configuration files</a>. This argument accepts a colon-separated list of directories.  All  configuration files (`*.tpc`) will be loaded from each path, with decreasing precedence.  The default is `.:$TAILPIPE_INSTALL_DIR/config` (`.:~/.tailpipe/config`).  This allows you to manage your <a href="/docs/reference/config-files/workspace"> workspaces </a> and <a href="/docs/reference/config-files/connection">connections</a> centrally in the `~/.tailpipe/config` directory, but override them in the working directory / mod location if desired.
    </td> 
  </tr>


  <tr> 
    <td nowrap="true"> `--workspace	`  </td> 
    <td>  Sets the Tailpipe workspace profile. If not specified, the default workspace will be used if it exists. See <a href="/docs/reference/config-files/workspace">workspace</a> for details. </td> 
  </tr>

</table>



## Examples

Open an interactive query console:
```bash
tailpipe query
```

Run a specific query directly:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log"
```

Query from a date:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log" --from 2025-01-01
```

Query within a range of dates:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log" --from 2025-01-01 --to 2025-01-31
```

Query a specific partition:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log --partition flaws
```

Query two partitions:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log --partition flaws,prod
```

Query a specific index:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log --index 605491513981
```

Run a query and save a [snapshot](/docs/query/snapshots):
```bash
tailpipe query --snapshot "select * from aws_cloudtrail_log"
```

Run a query and share a snapshot:
```bash
tailpipe query --share "select * from aws_cloudtrail_log"
```

Run the SQL command in the `my_queries/my_query.sql` file:
```bash
tailpipe query my_queries/my_query.sql
```

```
Run a query and return output in CSV format:
```bash
tailpipe query "select * from aws_cloudtrail_log limit 1000" --output csv
```

Run a query and return output in pipe-separated format:
```bash
tailpipe query "select * from aws_cloudtrail_log limit 1000" --output csv --separator '|'
```


