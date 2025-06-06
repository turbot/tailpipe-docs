---
title: Tips and Tricks
---

# Tips and Tricks

## Take advantage of hive partitioning

You can speed up a query by using a `where` or `join` clause to restrict the number of files Tailpipe will read to satisfy the query. This restriction operates at three levels.

*Partition*. When a table defines more than one partition, you can filter to include only files belonging to that partition.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod'
```

*Index*. When a partition defines more than one index, you can filter to include all files belonging to that index.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod' and index = 123456789
```

*Date*. Each file contains log data for one day. You can filter to include only files for that day.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod' and index = 123456789 and tp_date = '2024-12-01'
```

The [hive directory structure](/docs/collect/configure#hive-partitioning) enables you to exclude large numbers of Parquet files.

## Use common columns

Tailpipe plugins map a subset of log-specific fields to [common columns](/docs/reference/config-files/table#common-columns). Use them to search, join, or correlate across tables, for example, to search `aws_cloudtrail_log` and `aws_clb_access_log` for specific IP ranges.

```sql
with logs as (
  select
    tp_timestamp,
    tp_table,
    tp_partition,
    tp_source_ip,
    tp_source_location
  from
    aws_clb_access_log
  union all select
    tp_timestamp,
    tp_table,
    tp_partition,
    tp_source_ip,
    tp_source_location
  from
    aws_cloudtrail_log
)
select * from logs where try_cast(tp_source_ip as inet) <<= '13.58.0.0/15'::inet
```

## Use JSON functions vs operators

In DuckDB, `AND` and `OR` have higher precedence than `->` and `->>` (and other keywords), so they’re evaluated first.
As a result, this query will result in an error: *Conversion Error: Failed to cast value to numerical: …*.

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
select
  *
from
  aws_cloudtrail_log
where
  event_name = 'AddUserToGroup'
  and (request_parameters ->> 'groupName') ilike '%admin%'
order by
  event_time desc;
```

However, you may prefer to avoid the problem by using a JSON function.

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
