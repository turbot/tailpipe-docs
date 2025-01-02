---
title: It's Just SQL!
---

# It's Just SQL!

Tailpipe leverages DuckDB to provide a SQL interface to logs source from files, APIs, or S3 buckets.

## Basic SQL

Like most popular databases, DuckDB supports standard SQL syntax. If you know SQL, you already know how to query your logs!

You can **query all the columns** in a table:
```sql
select * from aws_cloudtrail_log limit 1000;
```

You can **filter** rows where columns only have a specific value: 
```sql
select
  tp_partition,
  tp_date,
  aws_region,
  event_type
from
  aws_cloudtrail_log
where
  event_type = 'AwsApiCCall';
```

or a **range** of values:

```sql
select
  tp_partition,
  tp_date,
  aws_region,
  event_type
from
  aws_cloudtrail_log
where
  aws_region in ('us-east-1', 'us-west-1');
```

or match a **pattern**: 

```sql
select
  tp_partition,
  tp_date,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log
where
  event_name ilike '%update%';
```

You can **filter on multiple columns**, joined by `and` or `or`:

```sql
select
  tp_partition,
  tp_date,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log
where
  event_name = 'UpdateTrail'
  and tp_date > date '2024-11-06';
```

You can **sort** your results:

```sql
select
  tp_partition,
  tp_date,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log
order by
  aws_region;
```

You can **sort on multiple columns, ascending or descending**:

```sql
select
  tp_partition,
  tp_date,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log
order by
  aws_region asc,
  tp_date desc;
```

You can group and use standard aggregate functions. You can **count** results:

```sql
select
  event_name,
  count(*) as count
from
  aws_cloudtrail_log
group by
  event_name
order by
  count desc;
```

or **sum** them:
```sql
select
  method,
  sum(body_bytes_sent) as total_bytes
from
  nginx_access_log
group by
  method;
```

or find **min**, **max**, and **average**:
```sql
select
  method,
  min(body_bytes_sent) as min_bytes,
  max(body_bytes_sent) as max_bytes,
  avg(body_bytes_sent) as avg_bytes
from
  nginx_access_log
group by
  method;
```

You can **exclude duplicate rows**:
```sql
select distinct
  event_name
from
  aws_cloudtrail_log;
```

or exclude **all but one matching row**:
```sql
select distinct on (event_type)
  tp_partition,
  tp_date,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log;
```

