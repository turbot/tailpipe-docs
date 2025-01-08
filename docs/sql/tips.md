---
title: Tips and Tricks
---

# Tips and Tricks

## Take advantage of hive partitioning

You can speed up a query by using a `where` or `join` clause to restrict the number of files Tailpipe will read to satisfy the query. This restriction operates at three levels.

*Partition*. When a table defines more than one partition, you can filter to include only files belonging to other partitions.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod'
```

*Index*. When a partition defines more than one index, you can filter to include  all files belonging to other indexes.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod' and index = 123456789
```

*Date*. Each file contains log data for one day. You can filter to include only files for that day.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod' and index = 123456789 and tp_date = '2024-12-01'
```

## Use pre-filtering

The `tailpipe connect` command can optionally restrict the set of files that Tailpipe reads to satisfy queries. For example, `tailpipe connect --from T-45d` yields a database connection that includes only the most recent 45 days of log data. Note that this restriction applies to all tables visible through that connections. You can further restrict the files read by Tailpipe using `where` or `join` filters in queries against that connection.

> [!NOTE]
> i'm aware that powerpipe uses this under the covers. it's unclear how i'd use it directly. i can do this
> tailpipe connect --from 2024-12-01
> /home/jon/.tailpipe/data/default/tailpipe_20250108123401.db
> and i can connect duckdb to that thing and query it
> how would i do so with tailpipe?

## Use JSON functions vs operators

In DuckDB, AND and OR have a higher precedence than `->` and `->>` (and other keywords), so they’re evaluated first.
So this query will result in an error: *Conversion Error: Failed to cast value to numerical: …*.

```sql
select
  *
from
  aws_cloudtrail_log
where
  event_name = 'AddUserToGroup'
  and request_parameters ->> 'groupName' ilike '%admin%'
order by
  event_time desc;
```

You can work around the problem by parenthesizing the JSON expression.

```sql
  and ( request_parameters ->> 'groupName' ) ilike '%admin%'
```

But you may prefer to avoid the problem by using a JSON function.

```sql
select
  *
from
  aws_cloudtrail_log
where
  event_name = 'AddUserToGroup'
  and json_extract_string(request_parameters, '$.groupName') ilike '%admin%'
order by
  event_time desc;
```



