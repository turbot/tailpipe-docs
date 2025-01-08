---
title: Tips and Tricks
---

# Tips and Tricks

## Take advantage of hive partitioning

You can speed up a query by using a `where` or `join` clause to restrict the number of files Tailpipe will read to satisfy the query. This restriction operates at three levels.

1. Partition. When a table defines more than one partition, you can filter to include only files belonging to other partitions.

2. Index. When a partition defines more than one index, you can filter to include  all files belonging to other indexes.

3. Date. Each file contains log data for one day. You can filter to include only files for that day.

## Use pre-filtering

The `tailpipe connect` command can optionally use `--from` and/or `--to` arguments to globally restrict the set of files that Tailpipe reads to satisfy queries. For example, `tailpipe connect --from T-45d` yields a database connection that includes only the most recent 45 days of log data. Note that this restriction applies to all tables visible through that connections. You can further restrict the files read by Tailpipe using `where` or `join` filters in queries against that connection.

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



