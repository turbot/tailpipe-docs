---
title: tailpipe query
---

# tailpipe query

>[!NOTE]
> Cloned from Steampipe with minor variation: show is new, no service mode
> snapshot stuff out of scope?
> pipes also? currently i can snap to pipes but nothing to see there with no engine behind

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

| Argument  |Description  
|--|--
| `--export string`              | Export query output to a file. You may export multiple output formats by entering multiple `--export` arguments. If a file path is specified as an argument, its type will be inferred by the suffix. Supported export formats are `sps` (`snapshot`). 
| `--header string`              | Specify whether to include column headers in csv and table output (default true`)|
| `--help`                       | Help for `tailpipe query`.
| `--output string`              | Select the console output format. Possible values are `line, csv, json, table, snapshot` (default `table`).
| `--pipes-host`                 | Sets the Turbot Pipes host used when connecting to Turbot Pipes workspaces. See [PIPES_HOST](reference/env-vars/pipes_host) for details. 
  `--pipes-token`                | Sets the Turbot Pipes authentication token used when connecting to Turbot Pipes workspaces. See [PIPES_TOKEN](reference/env-vars/pipes_token) for details.
| `--progress`                   | Enable or disable progress information. By default, progress information is shown - set `--progress=false` to hide the progress bar.
| `--query-timeout int`          | The query timeout, in seconds. The default is `0` (no timeout).
| `--search-path strings`        | Set a comma-separated list of connections to use as a custom [search path](managing/connections#setting-the-search-path) for the query session.
| `--search-path-prefix strings` | Set a comma-separated list of connections to use as a prefix to the current [search path](managing/connections#setting-the-search-path) for the query session.
| `--separator string`           | A single character to use as a separator string for csv output (defaults to `","`).
| `--share`                      | Create snapshot in Turbot Pipes with `anyone_with_link` visibility.
| `--snapshot`                   | Create snapshot in Turbot Pipes with the default (`workspace`) visibility.
| `--snapshot-location string`   | The location to write snapshots - either a local file path or a Turbot Pipes workspace.
| `--snapshot-tag string=string` | Specify tags to set on the snapshot. Multiple `--snapshot-tag` arguments may be passed.
| `--snapshot-title string`      | The title to give a snapshot when uploading to Turbot Pipes.
| `--timing=string`              | Enable or disable query execution timing: `off` (default), `on`, or `verbose`.
| `--workspace-database`         | Sets the database that Tailpipe will connect to. This can be `local` (the default) or a remote Turbot Pipes database. See [STEAMPIPE_WORKSPACE_DATABASE](/docs/reference/env-vars/steampipe_workspace_database) for details. 



## Examples

Open an interactive query console:
```bash
tailpipe query
```

Run a specific query directly:
```bash
tailpipe query "select * from aws_cloudtrail_log"
```

Run a query and save a [snapshot](/docs/snapshots/batch-snapshots):
```bash
tailpipe query --snapshot "select * from aws_cloudtrail_log"
```

Run a query and share a [snapshot](/docs/snapshots/batch-snapshots):
```bash
tailpipe query --share "select * from aws_cloudtrail_log"
```

Run the SQL command in the `my_queries/my_query.sql` file:
```bash
tailpipe query my_queries/my_query.sql
```

Run the SQL commands in all `.sql` files in the `my_queries` directory and concatenate the results:
```bash
tailpipe query my_queries/*.sql
```

Run a query and report the query execution time:
```bash
tailpipe query "select * from aws_cloudtrail_log" --timing
```

Run a query and report the query execution time and details for each scan:
```bash
tailpipe query "select * from aws_cloudtrail_log" --timing=verbose
```

Run a query and return output in json format:
```bash
tailpipe query "select * from aws_cloudtrail_log" --output json
```

Run a query and return output in CSV format:
```bash
tailpipe query "select * from aws_cloudtrail_log" --output csv
```

Run a query and return output in pipe-separated format:
```bash
tailpipe query "select * from aws_cloudtrail_log" --output csv --separator '|'
```


