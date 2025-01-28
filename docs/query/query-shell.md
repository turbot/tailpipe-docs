---
title: Interactive Queries
---

# Interactive Query Shell
Tailpipe provides an interactive query shell that provides features like auto-complete, syntax highlighting, and command history to assist you in [writing queries](/docs/sql).

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

![](/shell/metacommands.png)

As you continue to type, the autocomplete will continue to narrow down the list of metacommands to only those that match. You can cycle forward through the list with the `Tab` key, or backward with `Shift+Tab`. 


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

Tailpipe **tables** provide an interface for querying log data using standard SQL.  Tailpipe tables do not actually *store* data, they query the DuckDB views created over Parquet files collected by `tailpipe collect`. The details are hidden from you though - *you just query them like any other table!*

In the query shell, use `.inspect` to view tables. 

```bash
> .inspect
Table              Plugin    
aws_cloudtrail_log aws@local 
```

Select a table to view its columns.

```bash
> .inspect aws_cloudtrail_log
Column                          Type      
additional_event_data           json      
api_version                     varchar   
aws_region                      varchar   
edge_device_details             json      
error_code                      varchar   
error_message                   varchar   
event_category                  varchar   
event_id                        varchar   
event_name                      varchar   
event_source                    varchar   
event_time                      timestamp 
event_type                      varchar   
event_version                   varchar   
management_event                boolean   
read_only                       boolean   
recipient_account_id            varchar   
request_id                      varchar   
request_parameters              json      
resources                       json      
response_elements               json      
service_event_details           json      
session_credential_from_console varchar   
shared_event_id                 varchar   
source_ip_address               varchar   
tls_details                     struct    
tp_akas                         varchar[] 
tp_date                         date      
tp_destination_ip               varchar   
tp_domains                      varchar[] 
tp_emails                       varchar[] 
tp_id                           varchar   
tp_index                        varchar   
tp_ingest_timestamp             timestamp 
tp_ips                          varchar[] 
tp_partition                    varchar   
tp_source_ip                    varchar   
tp_source_location              varchar   
tp_source_name                  varchar   
tp_source_type                  varchar   
tp_table                        varchar   
tp_tags                         varchar[] 
tp_timestamp                    timestamp 
tp_usernames                    varchar[] 
user_agent                      varchar   
user_identity                   struct    
vpc_endpoint_id                 varchar  
```

