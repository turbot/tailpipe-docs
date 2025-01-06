---
title: Querying JSON and STRUCT
---

# Querying STRUCT and JSON Columns

Logs can contain complex data represented as JSON. Tailpipe plugins store such objects as one of two native DuckDB types: STRUCT or JSON.

## Accessing STRUCT elements

When all instances of the object have a regular shape, a plugin uses DuckDB's STRUCT type. The `user_identity` column of the `aws_cloudtrail_log` table is a STRUCT column, as you can verify using the `typeof` function.

```sql
select typeof(user_identity) from aws_cloudtrail_log limit 1;
typeof(user_identity) = STRUCT("type" VARCHAR, principal_id VARCHAR, arn VARCHAR, account_id VARCHAR, access_key_id VARCHAR, user_name VARCHAR, session_context STRUCT(attributes STRUCT(mfa_authenticated VARCHAR, creation_date BIGINT), ...
```

Dot notation is the native method for accessing fields in structs. An element extracted by the `.` operator has the type defined by the STRUCT. Because the `invoked_by` field is of type VARCHAR, you can compare it to a string.

```sql
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity.invoked_by = 'AWS Internal'
limit 1;
```

## Accessing JSON elements

When an object's instances have irregular shape, a plugin uses DuckDB's JSON type. The `request_parameters` column of the `aws_cloudtrail_log` table is a JSON column, as you can again verify using the `typeof` function.

```sql
select typeof(request_parameters) from aws_cloudtrail_log limit 1;
JSON
```

The `->` and `->>` operators work with JSON columns. To compare the `Host` field to a string, use `->>` which returns a stringified representation of the JSON value.

## Accessing arrays

Use 1-based indexing for STRUCT arrays and 0-based for JSON arrays. For example, here are two equivalent columns.


```sql
CREATE TABLE example (
    struct_col STRUCT(
        num DOUBLE,
        text STRING,
        string_array STRING[]
    ),
    json_col JSON
);

INSERT INTO example VALUES
(
    {num: 42.0, text: 'Hello', string_array: ['one', 'two', 'three']},
    '{"num": 42.0, "text": "Hello", "string_array": ["one", "two", "three"]}'
);
```

To access the first element of the STRUCT's array:

```sql
select struct_col.string_array[1] from example;
+----------------------------+
| one                        |
+----------------------------+
```

To access the first element of the JSON array:

```sql
select json_col->'string_array'->>0 from example;
+----------------------------+
| one                        |
+----------------------------+
```

Note the distinction between the `->` and `->>` operators. The type of `json_col->'string_array'` is JSON. If we instead accessed `json_col->>'string_array'` the type would be VARCHAR, and the result would be a string that we cannot index into. Similarly the type of `json_col->'string_array'->>0` is VARCHAR, and the result is a string we can compare to another string. If we instead accessed `json_col->'string_array'->>0` the type would be JSON. The printed value would look the same (*one*), but this result would not be comparable to a string.


