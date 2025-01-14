---
title: Querying IP Addresses
---


# Querying IP Addresses

IP addresses are fundamental to security-oriented log analysis. Tailpipe includes DuckDB's [inet extension](https://duckdb.org/docs/extensions/inet.html) which supports CIDR notation.

You can find requests **from a specific IP address**:

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

You can find requests **to or from an IP address**:

```sql
select
  event_type,
  tp_source_ip,
  tp_destination_ip
from
  aws_cloudtrail_log
where
  '157.131.52.10' in tp_ips
```

You can find requests from IPs that are **within a specific network range**:

```sql
select
  event_type,
  tp_source_ip
from
  aws_cloudtrail_log
where
  tp_source_ip <<= '114.33.0.0/16'::inet
```

You can find requests **not from internal networks**:

```sql
select
  tp_source_ip,
  count(*) as request_count
from
  aws_cloudtrail_log
where
  not (remote_addr::inet <<= '10.0.0.0/8'::inet)
  and not (remote_addr::inet <<= '192.168.0.0/16'::inet)
  and not (remote_addr::inet <<= '172.16.0.0/12'::inet)
group by
  tp_source_ip
order by
  request_count desc;
```


