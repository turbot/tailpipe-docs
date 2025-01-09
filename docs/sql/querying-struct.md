---
title: Querying STRUCT
---

# Querying STRUCT Columns

Logs can contain complex data represented as JSON. Tailpipe plugins store such objects as one of two native DuckDB types: STRUCT or JSON. Learn about STRUCT idioms here, see [Querying JSON Columns](/docs/sql/querying-json) for JSON idioms.

When all instances of the object have a regular shape, a plugin uses DuckDB's STRUCT type. The `user_identity` column of the `aws_cloudtrail_log` table is a STRUCT column, as you can verify using the `typeof` function.

```sql
select typeof(user_identity) from aws_cloudtrail_log limit 1;
typeof(user_identity) = STRUCT("type" VARCHAR, principal_id VARCHAR, arn VARCHAR, \
  account_id VARCHAR, access_key_id VARCHAR, user_name VARCHAR, \ 
  session_context STRUCT(attributes STRUCT(mfa_authenticated VARCHAR, creation_date BIGINT), \
  ...
```

>[!NOTE]
> if .inspect is available, that'll be the preferred way to observe the type

DuckDB doesn't have a `struct_keys` function analogous to `json_keys` but you can list the keys of STRUCT by casting to JSON

```sql
select json_keys( json(user_identity) ) from aws_cloudtrail_log limit 1;
```


The `user_identity` column includes an `invoked_by` field that you can extract using dot notation:

```sql
select user_identity.invoked_by from aws_cloudtrail_log
```

Because the STRUCT-defined type of `invoked_by` field is VARCHAR, you can compare it to a string.

```sql
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity.invoked_by = 'AWS Internal'
```

The `user_identity` column includes a nested STRUCT, `session_context`. You can use dot notation to drill into it:

```
select user_identity.session_context.attributes.mfa_authenticated
from aws_cloudtrail_log
```


