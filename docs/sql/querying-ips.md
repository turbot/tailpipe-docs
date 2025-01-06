---
title: Querying IP Addresses
---

Tailpipe includes DuckDB's [inet extension](https://duckdb.org/docs/extensions/inet.html) which supports CIDR notation.

# Querying IP Addresses

IP addresses are fundamental to security-oriented log analysis. 

You can **find traffic from specific IP addresses**:

```sql
select
  tp_partition,
  tp_date,
  aws_region,
  event_type
from
  aws_cloudtrail_log
where
  tp_source_ip = '114.33.107.149';
```

You can find requests from IPs that are **within a specific network range**:

```sql
select
  remote_addr,
  count(*) as request_count,
  sum(body_bytes_sent) as total_bytes
from
  nginx_access_log
where
  remote_addr::inet <<= '114.33.0.0/16'::inet
group by
  remote_addr
order by
  request_count desc;
```

You can find traffic that's **not from internal networks**:

```sql
select
  remote_addr,
  count(*) as request_count
from
  nginx_access_log
where
  not (remote_addr::inet <<= '10.0.0.0/8'::inet)
  and not (remote_addr::inet <<= '192.168.0.0/16'::inet)
  and not (remote_addr::inet <<= '172.16.0.0/12'::inet)
group by
  remote_addr
order by
  request_count desc;
```

