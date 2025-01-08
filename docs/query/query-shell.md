---
title: Interactive Queries
---

# Interactive Query Shell
Tailpipe provides an interactive query shell that provides features like auto-complete, syntax highlighting, and command history to assist you in [writing queries](/docs/sql/tailpipe-sql).

To open the query shell, run `tailpipe query`:

```bash
$ tailpipe query
>
```

Notice that the prompt changes, indicating that you are in the Tailpipe shell.

If you've collected a lot of data and want to optimize your queries for a subset of it, you can pre-filter the database. You can restrict to the most recent 45 days:

```bash
$ tailpipe query --from T-45d
```

Or to a range:

```
$ tailpipe query --from 2024-12-01 --to 2025-01-01
```

Or to a specific index in a partition:

```
$ tailpipe query --partition aws_cloudtrail_log.prod --index 123456789
```

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



## Exploring Tables & Columns

Tailpipe **tables** provide an interface for querying log data using standard SQL.  Tailpipe tables do not actually *store* data, they query the DuckDB views created over parquet files collected by `tailpipe collect`. The details are hidden from you though - *you just query them like any other table!*

### Tables

TBD

### Columns

TBD