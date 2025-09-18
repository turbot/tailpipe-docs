---
title: Query Tailpipe
---

# Powered by DuckDB + DuckLake!

Tailpipe [collects](/docs/collect/collect) logs into open parquet files and catalogs them with [DuckLake](https://ducklake.select/), so you query everything with [standard SQL syntax](https://duckdb.org/docs/sql/introduction.html). This brings a simple "lakehouse" model: open data files, a lightweight metadata catalog, and fast local analytics. 

- Open formats: data is stored as Parquet on disk.
- Cataloged: DuckLake tracks tables/columns/partitions for efficient queries.
- Fast by design: partition pruning and vectorized execution via DuckDB.
- SQL-first: use familiar DuckDB syntax, functions, and tooling.

It's easy to [get started writing queries](/docs/sql), and the [Tailpipe Hub](https://hub.tailpipe.io) provides ***hundreds of example queries*** that you can use or modify for your purposes.  There are [example queries for each table](https://hub.tailpipe.io/plugins/turbot/aws/tables/aws_cloudtrail_log) in every plugin, and you can also [browse, search, and view the queries](https://hub.tailpipe.io/mods/turbot/tailpipe-mod-aws-dections/queries) in every published mod!


## Interactive Query Shell
Tailpipe provides an [interactive query shell](query/query-shell) that provides features like auto-complete, syntax highlighting, and command history to assist you in writing queries.

To open the query shell, run `tailpipe query` with no arguments:

```bash
$ tailpipe query
>
```

Notice that the prompt changes, indicating that you are in the Tailpipe shell.

You can exit the query shell by pressing `Ctrl+d` on a blank line or using the `.exit` command.


## Non-interactive (batch) query mode
The Tailpipe interactive query shell is a great platform for exploring your data and developing queries, but Tailpipe is more than just a query shell!

Tailpipe allows you to [run a query in batch mode](query/batch-query) and write the results to standard output (stdout). This is useful if you wish to redirect the output to a file, pipe to another command, or export data for use in other tools.

To run a query from the command line, specify the query as an argument to `tailpipe query`:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log"
```



## AI Tools (MCP)

The [Tailpipe MCP server](query/mcp) transforms how you interact with your cloud and security data.  It brings the power of conversational AI to your security and cloud logs, allowing you to extract critical insights using plain English — no complex SQL required!

The MCP is packaged separately and runs as an integration in your AI tool, such as [Claude Desktop](https://claude.ai/download) or [Cursor](https://www.cursor.com/).

