---
title: Querying JSON 
---

# Querying JSON 

## Understanding the Context

### JSON Columns
- Represent data as key-value pairs in a flexible format.
- Values can be extracted as JSON or plain strings.
- DuckDB supports JSON-specific operators like `->`, `->>`, and functions like `json_extract` or `json_extract_string`.
- Tailpipe plugins produce JSON columns for irregularly-shaped data.


### Struct Columns
- Represent more rigidly defined key-value structures, similar to rows in a table.
- Dot notation (`.key`) is the native method for accessing fields in structs.
- Tailpipe plugins typically produce JSON columns for regularly-shaped data.

## Key Operators and Methods

### For JSON Columns

Example of a JSON column's type:
```sql
select typeof(request_parameters) from aws_cloudtrail_log limit 1;
JSON  
```

**Accessing Values**:
- `->`: Extracts a field as JSON. Use this when you want to retain the JSON type.
- `->>`: Extracts a field as a plain string. Use this when you need a direct value for comparisons or display.
- `json_extract` or `json_extract_string`: Use these for more complex extractions, including nested paths or computed keys.
- `json_object->'key'->0->>'value' extracts the string value of the zeroth element
- Access a JSON array element as JSON: 'key_with_array'->0
- Access a JSON array element as string: 'key_with_array'->>0

**Comparisons**:
- Always use `->>` or `json_extract_string` for comparisons with plain strings because `->` returns a JSON object that cannot be directly compared to strings.

Example:
```sql
-- This works because it accesses `Host` as a string
select count(*)
from aws_cloudtrail_log
where request_parameters->>'Host' = 'example.com';
```

```sql
-- This fails because it accesses `Host` as a JSON value
select count(*)
from aws_cloudtrail_log
where request_parameters->'Host' = 'example.com';
   ```

**Dot Notation**:
- For JSON columns, Tailpipe will allow an expression like `request_parameters.Host`, it generally behaves like `->` (returns JSON) and requires an explicit conversion for string comparisons.

Example:

```sql
select count(*) from aws_cloudtrail log 
where request_parameters->>'Host' = 'aws-cloudtrail-logs-605491513981-2755fe67.s3.us-east-1.amazonaws.com'
```

### For Struct Columns

Example of a STRUCT column's type

```sql
select typeof(user_identity) from aws_cloudtrail_log limit 1;
STRUCT("type" VARCHAR, principal_id VARCHAR, arn VARCHAR,
account_id VARCHAR, access_key_id VARCHAR, ...
```

**Accessing Values**:
- Dot notation (`.key`) is the default and most intuitive way to access fields within a struct.
- Dot notation directly extracts the value in its native type (e.g., string, number).

Example:
   
```
select count(user_identity.invoked_by) from aws_cloudtrail_log 
where user_identity->>'invoked_by' = 'AWS Internal';
```

**Comparisons**:
- Struct fields are directly comparable without additional casting.
- Dot notation simplifies field access for both selection and comparison.

**JSON-Like Operators**:
- Applicable to struct fields in DuckDB. You can use `->` to extract a field as its native type or `->>` to extract it as a plain string, just like with JSON columns. Structs, however, enforce stricter typing than JSON.

Example:
```sql
select count(user_identity->>'invoked_by') as invoked_by_count
from aws_cloudtrail_log
where user_identity->>'invoked_by' = 'AWS Internal';
```   


## Comparison: JSON vs Structs

| Aspect                      | JSON (e.g., `request_parameters`)     | Struct (e.g., `user_identity`)           |
|-----------------------------|---------------------------------------|------------------------------------------|
| **Field Access**            | `->` (JSON) or `->>` (string)         | `field.key`                              |
| **Field Extraction Type**   | JSON (`->`), String (`->>`)           | Native type of the field                 |
| **Comparison Readiness**    | Use `->>` or `json_extract_string`    | Direct comparison                        |
| **Complexity**              | Flexible, but needs explicit handling | Simpler, as types are already defined    |



