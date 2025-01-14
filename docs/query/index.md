---
title: Query Tailpipe
---

# It's just DuckDB!

Tailpipe [collects](/docs/manage/collection) logs into a [DuckDB](https://duckdb.org/) database that use [standard SQL syntax](https://duckdb.org/docs/sql/introduction.html) to query. It's easy to [get started writing queries](/docs/sql), and the [Tailpipe Hub](https://hub.tailpipe.io) provides ***hundreds of example queries*** that you can use or modify for your purposes.  There are [example queries for each table](https://hub.tailpipe.io/plugins/turbot/aws/tables/aws_cloudtrail_log) in every plugin, and you can also [browse, search, and view the queries](https://hub.tailpipe.io/mods/turbot/tailpipe-mod-aws-dections/queries) in every published mod!

## Basic SQL

If you know SQL, you already know how to query Tailpipe!

You can **query all the columns** in a table:
```sql
select * from aws_cloudtrail_log;
```

More often, you'll query a subset of columns:
```sql
select
  event_name,
  event_type,
  event_source,
  tp_date
from
  aws_cloudtrail_log;
```

You can **filter** rows where columns only have a specific value: 
```sql
select
  event_name,
  event_type,
  event_source,
  tp_date
from
  aws_cloudtrail_log
where
  event_source = 'kms.amazonaws.com'
```

or a **range** of values:
```sql
select
  event_name,
  event_type,
  event_source,
  tp_date
from
  aws_cloudtrail_log
where
  event_type in ('AwsApiCall', 'AwsServiceEvent');
```

or match a **pattern**: 

```sql
select
  event_name,
  tp_source_id,
  tp_date,
  request_parameters
from
  aws_cloudtrail_log
where
  user_identity.principal_id ilike '%turbot%';
```

You can **filter on multiple columns**, joined by `and` or `or`:

```sql
select
  event_name,
  tp_source_id,
  tp_date,
  request_parameters
from
  aws_cloudtrail_log
where
  user_identity.principal_id ilike '%turbot%'
  and event_category = 'Management'
```

You can **sort** your results:

```sql
select
  event_name,
  tp_source_id,
  tp_date,
  request_parameters
from
  aws_cloudtrail_log
where
  user_identity.principal_id ilike '%turbot%'
  and event_category = 'Management'
order by tp_date;
```

You can **sort on multiple columns, ascending or descending**:

```sql
select
  event_name,
  tp_source_id,
  tp_date,
  request_parameters
from
  aws_cloudtrail_log
order by
  event_name,
  tp_date desc;
```

You can group and use standard aggregate functions, for example to **count** results:

```sql
select
  event_type,
  count(*) as count
from
  aws_cloudtrail_log
group by
  event_type
order by
  count desc;
```

or find **min** and **max**:

```sql
select
  min(tp_date),
  max(tp_date)
from
  aws_cloudtrail_log;
```

You can **exclude duplicate rows**:

```sql
select distinct
  event_type
from
  aws_cloudtrail_log;
```

Of course the real power of SQL is in combining data from multiple tables!

You can **join tables** together on a key field:

```sql
select 
    alb.tp_source_ip,
    alb.tp_timestamp,
    alb.status_code,
    alb.request,
    alb.request_url,
    ct.tp_source_ip,
    ct.tp_timestamp,
    ct.event_name,
    ct.event_type
from 
    alb_access_log as alb
join 
    cloudtrail_log ct as ct
on
    alb.tp_source_ip = ct.tp_source_ip
where
    ct.tp_timestamp between alb.tp_timestamp - interval 5 minute
    and alb.tp_timestamp + interval 5 minute
```



## Interactive Query Shell
Tailpipe provides an [interactive query shell](query/query-shell) that provides features like auto-complete, syntax highlighting, and command history to assist you in writing queries.

To open the query shell, run `tailpipe query` with no arguments:

```bash
$ tailpipe query
>
```

Notice that the prompt changes, indicating that you are in the Tailpipe shell.

You can exit the query shell by pressing `Ctrl+d` on a blank line, or using the `.exit` command.


## Non-interactive (batch) query mode
The Tailpipe interactive query shell is a great platform for exploring your data and developing queries, but Tailpipe is more than just a query shell!

Tailpipe allows you to [run a query in batch mode](query/batch-query) and write the results to standard output (stdout). This is useful if you wish to redirect the output to a file, pipe to another command, or export data for use in other tools.

To run a query from the command line, specify the query as an argument to tailpipe query:
```bash
tailpipe query "select count(*) from aws_cloudtrail_log"
```



