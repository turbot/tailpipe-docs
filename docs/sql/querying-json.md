---
title: Querying JSON and STRUCT
---

# Querying JSON and STRUCT Columns

## JSON Columns
- Represent data as key-value pairs in a flexible format.
- JSON operators (`->`, `->>`) are the native method for accessing fields in structs. There also functions like `json_extract` and `json_extract_string`.
- But dot notation works too!
- Tailpipe plugins produce JSON columns for irregularly-shaped data.

## Struct Columns
- Represent more rigidly defined key-value structures, similar to rows in a table.
- Dot notation is the native method for accessing fields in structs.
- But JSON operators work too!
- Tailpipe plugins typically produce struct columns for regularly-shaped data.

## Key Operators and Methods

### For JSON Columns

Example of a JSON column's type:
```sql
select typeof(request_parameters) from aws_cloudtrail_log limit 1;
JSON
```

**Accessing Values**:
- `object->'key'`: Extracts a value as JSON. Use this when you want to retain the JSON type.
- `object.key`: Same as `object->`key`.
- `objecct->>'key'`: Extracts a value as a plain string. Use this when you need a primitive value for comparison or display.
- `object.key`: Same as `object->`key`.
- `object->'key_with_array'->0`: Extracts the zeroth element of an array value as JSON.
- `object->>'key_with_array'->>0`: Extracts the zeroth element of an array value as a string.

**Dot Notation**:
- For JSON columns, DuckDB supports dot notation, such as `request_parameters.Host`, which simplifies field access. It generally behaves like `->`.

Examples:
```sql
-- Fails because it accesses `Host` as JSON
select count(*)
from aws_cloudtrail_log
where request_parameters->'Host' = 'example.com';

-- Works because it accesses `Host` as a string
select count(*)
from aws_cloudtrail_log
where request_parameters->>'Host' = 'example.com';

-- Fails because it access `Host` as JSON
select request_parameters.Host = 'example.com'
from aws_cloudtrail_log
limit 1;
```

### For Struct Columns

Example of a STRUCT column's type:
```sql
select typeof(user_identity) from aws_cloudtrail_log limit 1;
typeof(user_identity) = STRUCT("type" VARCHAR, principal_id VARCHAR, arn VARCHAR, account_id VARCHAR, access_key_id VARCHAR, user_name VARCHAR, session_context STRUCT(attributes STRUCT(mfa_authenticated VARCHAR, creation_date BIGINT), 
```

**Accessing Values**:
- Dot notation is the default and most intuitive way to access fields within a struct. It extracts the value in its native type (e.g., string, number).

**JSON operators**:
- For STRUCT columns, DuckDB supports JSON operators as well as dot notation.

Examples:
```sql
-- Comparing to a string field with dot notation
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity.invoked_by = 'AWS Internal'
limit 1;

-- Comparing to a string field with a JSON operator
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity->>'invoked_by' = 'AWS Internal'
limit 1;

-- This fails because the left expression is JSON, not string
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity->'invoked_by' = 'AWS Internal'
limit 1;
```

**Comparisons**:
- Struct fields are directly comparable without additional casting.
- Dot notation simplifies field access for both selection and comparison.

**JSON-Like Operators**:
- Applicable to struct fields in DuckDB. You can use `->` to extract a field as its native type or `->>` to extract it as a plain string, similar to JSON columns.

## Key Distinction: Typing vs. Flexibility

**Struct Columns**: These are strongly typed. Each field in the struct has a defined type (e.g., VARCHAR, INTEGER), and operations on these fields respect that type. Using dot notation (field.key) provides straightforward access with clear typing.

Example:

STRUCT

```sql
-- Direct comparison of a numeric field in a struct
select count(*)
from aws_cloudtrail_log
where user_identity.account_id = 123456789012;

JSON

-- JSON requires casting the value to a number for comparison
select count(*)
from aws_cloudtrail_log
where request_parameters->>'account_id'::BIGINT = 123456789012;
```

**JSON Columns**: These are flexible and loosely typed. A JSON column stores raw JSON data, and fields must be interpreted at runtime. JSON operators (`->`, `->>`) are the native way to extract and transform this data, but dot notation also works for convenience in many cases.

Example:

JSON

```sql
-- Access a field that may or may not exist
select request_parameters->>'optional_field'
from aws_cloudtrail_log;
```

STRUCT

```sql
-- Structs require the field to exist in the schema
-- This query fails if 'optional_field' is missing from the struct
select user_identity.optional_field
from aws_cloudtrail_log;
```


