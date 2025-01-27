---
title: Tips and Tricks
---

# Tips and Tricks

## Use WHERE clauses to filter by partition, index, or date.

The [hive directory structure](docs/manage/partition) enables you to exclude large numbers of Parquet files.

## Use common fields

Tailpipe plugins map a subset of log-specific fields to [common fields](/docs/manage/collection#common-fields). Use them to correlate across tables, for example to join `aws_cloudtrail_log` and `aws_alb_access_log` on the `tp_ip` field (IP address).

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



