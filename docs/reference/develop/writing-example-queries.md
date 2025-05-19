---
title: Writing Example Queries
sidebar_label: Writing Example Queries
---
# Writing Example Queries
- [Sample Guidelines](#sample-guidelines)
- [Basic Examples](#basic-examples)
- [Advanced Examples](#advanced-examples)

## Sample Guidelines
To help you get started on creating useful example queries for Tailpipe log tables, we've compiled a
list of potential topics that can be used as guidelines and includes some
basic and advanced examples of these from existing tables.

Please note though that the topics below are just suggestions and example
queries are not limited to just these topics.

- Security and Compliance
  - Failed authentication attempts
  - Suspicious activity detection
  - Privilege escalation events
  - Compliance violations
  - Unauthorized access attempts
- Operations and Troubleshooting
  - Error rate analysis
  - Performance bottlenecks
  - Service availability
  - Request latency
  - Resource utilization
- User Behavior
  - Traffic patterns
  - User activity trends
  - Geographic distribution
  - Session analysis
- Anomaly Detection
  - Unusual access patterns
  - Unexpected traffic spikes
  - Deviation from baseline behavior
  - Rare event correlation

## Basic Examples

### aws_alb_access_log
#### Basic info
```sql
select
  timestamp,
  elb,
  client_ip,
  request_url,
  elb_status_code
from
  aws_alb_access_log
limit 10;
```

#### List failed HTTP requests
```sql
select
  timestamp,
  elb,
  client_ip,
  target_ip,
  elb_status_code,
  request_url,
  request_http_method
from
  aws_alb_access_log
where
  elb_status_code >= 400
order by
  timestamp desc
limit 20;
```

#### Count requests by status code
```sql
select
  elb_status_code,
  count(*) as request_count
from
  aws_alb_access_log
group by
  elb_status_code
order by
  request_count desc;
```

### azure_activity_log
#### Basic info
```sql
select
  event_timestamp,
  operation_name,
  caller,
  resource_id
from
  azure_activity_log
limit 10;
```

#### List recent role assignment changes
```sql
select
  event_timestamp,
  resource_id,
  caller,
  resource_group_name,
  subscription_id
from
  azure_activity_log
where
  operation_name = 'Microsoft.Authorization/roleAssignments/write'
order by
  event_timestamp desc
limit 20;
```

#### Count events by operation type
```sql
select
  operation_name,
  count(*) as event_count
from
  azure_activity_log
group by
  operation_name
order by
  event_count desc
limit 10;
```

### gcp_audit_log
#### Basic info
```sql
select
  timestamp,
  resource_name,
  method_name,
  principal_email
from
  gcp_audit_log
limit 10;
```

#### List failed authentication attempts
```sql
select
  timestamp,
  resource_name,
  method_name,
  principal_email,
  status_message
from
  gcp_audit_log
where
  status_code != 0
order by
  timestamp desc
limit 20;
```

#### Count events by service
```sql
select
  service_name,
  count(*) as event_count
from
  gcp_audit_log
group by
  service_name
order by
  event_count desc
limit 10;
```

### nginx_access_log
#### Basic info
```sql
select
  time_local,
  remote_addr,
  request,
  status,
  body_bytes_sent
from
  nginx_access_log
limit 10;
```

#### List 404 errors
```sql
select
  time_local,
  remote_addr,
  request,
  http_referer
from
  nginx_access_log
where
  status = 404
order by
  time_local desc
limit 20;
```

#### Count requests by status code
```sql
select
  status,
  count(*) as request_count
from
  nginx_access_log
group by
  status
order by
  request_count desc;
```

## Advanced Examples

### Analyzing slow requests in AWS ALB logs
#### Identify and analyze slow requests
```sql
select
  timestamp,
  elb,
  request_url,
  request_http_method,
  client_ip,
  target_ip,
  request_processing_time,
  target_processing_time,
  response_processing_time,
  (request_processing_time + target_processing_time + response_processing_time) as total_time
from
  aws_alb_access_log
where
  (request_processing_time + target_processing_time + response_processing_time) > 1
order by
  total_time desc
limit 10;
```

### Detecting suspicious activity in Azure Activity logs
#### Detect unusual administrative actions
```sql
with baseline as (
  select
    caller,
    operation_name,
    count(*) as typical_count
  from
    azure_activity_log
  where
    event_timestamp < current_date - interval '7' day
  group by
    caller,
    operation_name
)
select
  a.event_timestamp,
  a.caller,
  a.operation_name,
  a.resource_id,
  count(*) as today_count,
  b.typical_count
from
  azure_activity_log a
left join
  baseline b
  on a.caller = b.caller and a.operation_name = b.operation_name
where
  a.event_timestamp >= current_date - interval '1' day
group by
  a.event_timestamp,
  a.caller,
  a.operation_name,
  a.resource_id,
  b.typical_count
having
  count(*) > coalesce(b.typical_count * 3, 10)
order by
  today_count desc;
```

### Correlating GCP audit logs with resource changes
#### Track resource creation and deletion events
```sql
select
  timestamp,
  resource_name,
  method_name,
  principal_email,
  case
    when method_name like '%create%' then 'creation'
    when method_name like '%delete%' then 'deletion'
    when method_name like '%update%' then 'update'
    else 'other'
  end as operation_type
from
  gcp_audit_log
where
  method_name like '%create%'
  or method_name like '%delete%'
  or method_name like '%update%'
order by
  timestamp desc
limit 50;
```

### Analyzing web traffic patterns in Nginx logs
#### Analyze traffic by hour of day
```sql
select
  extract(hour from time_local) as hour_of_day,
  count(*) as request_count
from
  nginx_access_log
group by
  hour_of_day
order by
  hour_of_day;
```

#### Identify top referrers by traffic volume
```sql
select
  http_referer,
  count(*) as request_count
from
  nginx_access_log
where
  http_referer != '-'
group by
  http_referer
order by
  request_count desc
limit 20;
```

### Cross-plugin analysis for comprehensive security monitoring
#### Correlate authentication failures across platforms
```sql
-- AWS CloudTrail authentication failures
select
  'AWS' as platform,
  timestamp as event_time,
  event_name as event,
  error_code,
  error_message,
  user_identity_arn as user
from
  aws_cloudtrail_log
where
  error_code is not null
  and error_code like '%AccessDenied%'

union all

-- Azure authentication failures
select
  'Azure' as platform,
  event_timestamp as event_time,
  operation_name as event,
  status_code as error_code,
  status_value as error_message,
  caller as user
from
  azure_activity_log
where
  status_code != 'Succeeded'
  and operation_name like '%authenticate%'

union all

-- GCP authentication failures
select
  'GCP' as platform,
  timestamp as event_time,
  method_name as event,
  status_code::text as error_code,
  status_message as error_message,
  principal_email as user
from
  gcp_audit_log
where
  status_code != 0
  and method_name like '%authenticate%'

order by
  event_time desc
limit 50;
```

## DuckDB-Specific Considerations

When writing example queries for Tailpipe, keep in mind that Tailpipe uses DuckDB as its SQL engine, which has some differences from PostgreSQL (used by Steampipe):

1. **Date/Time Functions**: DuckDB uses functions like `date_trunc()` and `extract()` for date manipulation, similar to PostgreSQL but with some syntax differences.
2. **JSON Handling**: DuckDB uses `->>` for JSON string extraction and `->` for JSON object extraction, similar to PostgreSQL. However, some advanced JSON functions may differ.
3. **Regular Expressions**: Use the `regexp_matches()` function for pattern matching in DuckDB.
4. **Window Functions**: DuckDB supports window functions like `row_number()`, `rank()`, and `dense_rank()` with syntax similar to PostgreSQL.
5. **Type Casting**: Use the `::` operator for type casting (e.g., `value::integer`).

For more details on DuckDB's SQL syntax, refer to the [DuckDB SQL documentation](https://duckdb.org/docs/sql/introduction).
