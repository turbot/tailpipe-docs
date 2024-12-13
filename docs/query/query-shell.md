---
title: Interactive Queries
---

>[!NOTE]
> cloned from steampipe, with minor variation. how much is applicable?


# Interactive Query Shell
Tailpipe provides an interactive query shell that provides features like auto-complete, syntax highlighting, and command history to assist you in [writing queries](/docs/sql/tailpipe-sql).

To open the query shell, run `tailpipe query` with no arguments:

```bash
$ tailpipe query
>
```

Notice that the prompt changes, indicating that you are in the Tailpipe shell.

You can exit the query shell by pressing `Ctrl+d` on a blank line, or using the `.exit` command.


### Autocomplete
The query shell includes an autocomplete feature that will suggest words as you type.  Type `.` (period). Notice that the autocomplete appears with a list of the [Tailpipe meta-commands](/docs/reference/dot-commands/overview) commands that start with `.`:

![](/images/docs/auto-complete-1.png)

As you continue to type, the autocomplete will continue to narrow down the list of tables to only those that match.

You can cycle forward through the list with the `Tab` key, or backward with `Shift+Tab`.  Tab to select `.tables` and hit enter.  The `.tables` command is executed, and lists all the tables that are installed and available to query.


### History
The query shell supports command history, allowing you to retrieve, run, and edit previous commands.  The command history works like typical unix shell command history, and persists across query sessions.  When on a new line, you can cycle back through the history with the `Up Arrow` or `Ctrl+p` and forward with `Down Arrow` or `Ctrl+n`.


### Key bindings
The query shell supports standard emacs-style key bindings:

| Keys | Description
|-|-
| `Ctrl+a` |	Move the cursor to the beginning of the line
| `Ctrl+e` |	Move the cursor to the end of the line
| `Ctrl+f` |	Move the cursor forward 1 character
| `Ctrl+b` |	Move the cursor backward 1 character
| `Ctrl+w` |	Delete a word backwards
| `Ctrl+d` |	Delete a character forwards.  On a blank line, `Ctrl+d` will exit the console
| `Backspace` | Delete a character backwards
| `Ctrl+p`, `Up Arrow` |	Go to the previous command in your history
| `Ctrl+n`, `Down Arrow` |	Go to the next command in your history



## Exploring Tables & Connections

### Connections

A Tailpipe **Connection** represents a set of tables for a single data source. Each connection is represented as a distinct Postgres schema.

A connection is associated with a single instance of a single [plugin](/docs/managing/plugins) type. The boundary and scope of the connection varies by plugin, but is typically aligned with the vendor's CLI tool or API:

- An `azure` connection contains tables for a single Azure subscription
- An `aws` connection contains tables for a single AWS account

To view the installed connections, you can use the `.connections` :

```
> .connections
+------------+--------------------------------------------------+
| Connection |                      Plugin                      |
+------------+--------------------------------------------------+
| aws        | hub.tailpipe.io/plugins/turbot/aws@latest       |
| github     | hub.tailpipe.io/plugins/turbot/github@latest    |
| tailpipe  | hub.tailpipe.io/plugins/turbot/tailpipe@latest |
+------------+--------------------------------------------------+

To get information about the tables in a connection, run '.inspect {connection}'
To get information about the columns in a table, run '.inspect {connection}.{table}'

```

Alternately, you can use `.inspect` command with no arguments.  The output is the same:
```
> .inspect
+------------+--------------------------------------------------+
| Connection |                      Plugin                      |
+------------+--------------------------------------------------+
| aws        | hub.tailpipe.io/plugins/turbot/aws@latest       |
| github     | hub.tailpipe.io/plugins/turbot/github@latest    |
| tailpipe  | hub.tailpipe.io/plugins/turbot/tailpipe@latest |
+------------+--------------------------------------------------+

To get information about the tables in a connection, run '.inspect {connection}'
To get information about the columns in a table, run '.inspect {connection}.{table}'

```

### Tables
Tailpipe **tables** provide an interface for querying logc data using standard SQL.  Tailpipe tables do not actually *store* data, they query the DuckDB views created over parquet files collected by `tailpipe collect`. The details are hidden from you though - *you just query them like any other table!*

>[!NOTE]
> will this stuff work the same as sp?


To view the tables provided by a plugin,  in all active connections, you can use the `.tables` command:

```
> .tables
 ==> aws
+----------------------------------------+--------------------------------+
|                 Table                  |          Description           |
+----------------------------------------+--------------------------------+
| aws_cloudtrail_log                     | AWS Cloudtrail logs            |
+----------------------------------------+--------------------------------+

 ==> github
+----------------------------------------+--------------------------------+
|                 Table                  |          Description           |
+----------------------------------------+--------------------------------+
| github_audit_log                       | GitHub audit logs              |
+----------------------------------------+--------------------------------+
...


### Columns
To get information about the **columns** in a table, run `.inspect {connection}.{table}`:

```
> .inspect aws_cloudtrail_log
+----------------------+-----------------------------+--------------------------------+
|        Column        |            Type             |          Description           |
+----------------------+-----------------------------+--------------------------------+
| partition            | text                        | The name of a [partition](TBD) |
+----------------------+-----------------------------+--------------------------------+
```
