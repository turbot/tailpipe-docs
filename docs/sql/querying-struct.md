---
title: Querying STRUCT
---

# Querying STRUCT Columns

Logs can contain complex data represented as JSON. Tailpipe plugins store such objects as one of two native DuckDB types: [STRUCT](https://duckdb.org/docs/stable/sql/data_types/struct.html#retrieving-from-structs) or [JSON](https://duckdb.org/docs/stable/data/json/overview.html#retrieving-json-data). Learn about STRUCT idioms here; see [Querying JSON Columns](/docs/sql/querying-json) for JSON idioms.

When all instances of the object have a regular shape, a plugin uses DuckDB's STRUCT type. The `user_identity` column of the `aws_cloudtrail_log` table is a STRUCT column, as you can verify using the `typeof` function.

```sql
select typeof(user_identity) from aws_cloudtrail_log limit 1;
```

```sql
typeof(user_identity) = STRUCT("type" VARCHAR, principal_id VARCHAR, arn VARCHAR,
  account_id VARCHAR, access_key_id VARCHAR, user_name VARCHAR,
  session_context STRUCT(attributes STRUCT(mfa_authenticated VARCHAR, creation_date BIGINT),
  ...
```

DuckDB doesn't have a `struct_keys` function analogous to `json_keys`, but you can list the keys of STRUCT by casting to JSON:

```sql
select
  json_keys(json(user_identity))
from
  aws_cloudtrail_log
limit 1;
```

The `user_identity` column includes an `invoked_by` field that you can extract using dot notation:

```sql
select
  user_identity.invoked_by
from
  aws_cloudtrail_log;
```

Because the STRUCT-defined type of `invoked_by` field is VARCHAR, you can compare it to a string.

```sql
select
  user_identity.invoked_by
from
  aws_cloudtrail_log
where
  user_identity.invoked_by = 'AWS Internal';
```

The `user_identity` column includes a nested STRUCT, `session_context`. You can use dot notation to drill into it:

```sql
select
  user_identity.session_context.attributes.mfa_authenticated
from
  aws_cloudtrail_log;
```

You can also [unnest](https://duckdb.org/docs/stable/sql/query_syntax/unnest) items in a nested STRUCT to make them easier to work with:

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
        "domainName": "another.fqdn.",
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
    unnest(requestData.question) as q
  from
    my_dns
  where
    messageType = 'ClientQuery'
)
select
  q.questionType as question_type
from
  questions
where
  q.domainName = 'some.fqdn.';
```
