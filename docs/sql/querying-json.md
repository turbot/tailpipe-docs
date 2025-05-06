---
title: Querying JSON
---

# Querying JSON Columns

Logs can contain complex data represented as JSON. Tailpipe plugins store such objects as one of two native DuckDB types: [JSON](https://duckdb.org/docs/stable/data/json/overview.html#retrieving-json-data) or [STRUCT](https://duckdb.org/docs/stable/sql/data_types/struct.html#retrieving-from-structs). Learn about JSON idioms here; see [Querying STRUCT Columns](/docs/sql/querying-struct) for STRUCT idioms.

When an object's instances have an irregular shape, a plugin uses DuckDB's JSON type. The `request_parameters` column of the `aws_cloudtrail_log` table is a JSON column, as you can verify using the `typeof` function.

```sql
select
  typeof(request_parameters)
from
  aws_cloudtrail_log
limit 1;
```

```sql
JSON
```

You can list the keys of a JSON object:

```sql
select
  json_keys(request_parameters)
from
  aws_cloudtrail_log
limit 1;
```

The `request_parameters` column contains a JSON object that includes a `Host` key whose value you can extract as a VARCHAR with a function:

```sql
select
  json_extract_string(request_parameters, '$.Host')
from
  aws_cloudtrail_log;
```

Or with the JSON operator:

```sql
select
  request_parameters ->> 'Host'
from
  aws_cloudtrail_log;
```

Both methods return a string that you can compare.

```sql
select
  json_extract_string(request_parameters, '$.Host') ilike '%aws%'
from
  aws_cloudtrail_log;

select
  (request_parameters ->> 'Host') ilike '%aws%'
from
  aws_cloudtrail_log;
```

In this case, DuckDB's precedence rules require you to parenthesize the `->>` expression.

The `resource` column of `aws_cloudtrail_log` is a JSON array of objects. You use 0-based indexing to access elements of an array. To access the first element:

```sql
select
  resources[0]
from
  aws_cloudtrail_log;
```

Alternatively, you can use the JSON `->` operator:

```sql
select
  resources -> 0
from
  aws_cloudtrail_log;
```

Either method returns a JSON object that you can drill into.

You can also [unnest](https://duckdb.org/docs/stable/sql/query_syntax/unnest) a nested array of objects using [JSONPath](https://jsonpath.com/) to make them easier to work with:

```json
{
  "messageType": "ClientQuery",
  "requestData": {
    "fullRcode": 0,
    "question": [
      {
        "class": "IN",
        "domainName": "some.fqdn.",
        "questionType": "A",
        "questionTypeId": 1
      },
      {
        "class": "IN",
        "ednsCode": 8,
        "domainName": "another.fqdn.",
        "queryFlag": "recursionDesired",
        "questionType": "AAAA",
        "questionTypeId": 28
      }
    ]
  },
  "timestamp": "2025-02-24T11:40:10.193092152Z"
}
```

```sql
with questions as (
  select
    unnest(json_extract(requestData, '$.question[*]')) as q
  from
    my_dns
  where
    messageType = 'ClientQuery'
)
select
  json_extract_string(q, '$.questionType') as question_type
from
  questions
where
  json_extract_string(q, '$.domainName') = 'some.fqdn.';
```
