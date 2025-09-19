---
title: It's Just SQL!
---

# It's Just SQL!

Tailpipe leverages DuckDB to provide a SQL interface to the tables built from the log data you've collected.

## Basic SQL

Like most popular databases, DuckDB supports standard SQL syntax. If you know SQL, you already know how to query your logs!

You can **query all the columns** in a table:

```sql
select * from aws_cloudtrail_log;
```

But you may want to limit the number of rows when tables are large:

```sql
select * from aws_cloudtrail_log limit 1000;
```


You can **filter** rows where columns only have a specific value: 
```sql
select
  tp_partition,
  tp_timestamp,
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
  tp_timestamp,
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
  tp_timestamp,
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
  tp_timestamp,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log
where
  event_name = 'UpdateTrail'
  and tp_timestamp > date '2024-11-06';
```

You can **sort** your results:

```sql
select
  tp_partition,
  tp_timestamp,
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
  tp_timestamp,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log
order by
  aws_region asc,
  tp_timestamp desc;
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

or find **min** and **max**:
```sql
select
  min(tp_date),
  max(tp_date)
from
  aws_cloudtrail_log
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
  tp_timestamp,
  aws_region,
  event_type,
  event_name
from
  aws_cloudtrail_log;
```

