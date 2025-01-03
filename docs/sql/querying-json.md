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
select count(*)
from aws_cloudtrail_log
where request_parameters->>'Host' = 'example.com';

-- Works because it accesses `Host` as a string
```

---

```sql
select count(*) 
from aws_cloudtrail_log
where json_extract_string(request_parameters, 'Host') = 'example.com'

-- Works because it accesses `Host` as a string
```
---

```sql
select count(*)
from aws_cloudtrail_log
where request_parameters->'Host' = 'example.com';

-- Fails with a binder error
-- Binder Error: No function matches the given name and argument types 'json_extract(JSON, BOOLEAN)'
-- That's because DuckDB internally maps `->` to `json_extract` 
-- Why BOOLEAN? Apparently a type inference failure.
```

---

```sql
select request_parameters.Host = 'example.com'
from aws_cloudtrail_log
limit 1;

-- Fails with a conversion error because it access `Host` as JSON
```

---

```sql
select request_parameters.Host::string = 'example.com'
from aws_cloudtrail_log
limit 1;

-- Fails because although DuckDB reports VARCHAR as the
-- type of both expressions, the internal representations
-- don't match:
-- "s3.amazonaws.com" vs s3.amazonaws.com
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
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity.invoked_by = 'AWS Internal'
limit 1;

-- Works because the native type of `invoked_by` is string.
```

---

```sql
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity->>'invoked_by' = 'AWS Internal'
limit 1;

-- Works because the JSON `->>` operator returns a string.
```
---

```sql
select user_identity.invoked_by
from aws_cloudtrail_log
where user_identity->'invoked_by' = 'AWS Internal'
limit 1;

-- Fails because the `->` operator returns JSON, not string
```

**Comparisons**:
- Struct fields are directly comparable without additional casting.
- Dot notation simplifies field access for both selection and comparison.

**JSON-Like Operators**:
- Applicable to struct fields in DuckDB. You can use `->` to extract a field as its native type or `->>` to extract it as a plain string, similar to JSON columns.

## Key Distinction: Typing vs. Flexibility

**Struct Columns**: These are strongly typed. Each field in the struct has a defined type (e.g., VARCHAR, INTEGER), and operations on these fields respect that type. Using dot notation (`field.key`) provides straightforward access with clear typing.

Example:

STRUCT

```sql
select count(*)
from aws_cloudtrail_log
where user_identity.account_id = 123456789012;

-- Direct comparison of a numeric field in a struct
```

JSON




```sql
select count(*)
from aws_cloudtrail_log
where request_parameters->>'account_id'::BIGINT = 123456789012;

-- JSON requires casting the value to a number for comparison
```

**JSON Columns**: These are flexible and loosely typed. A JSON column stores raw JSON data, and fields must be interpreted at runtime. JSON operators (`->`, `->>`) are the native way to extract and transform this data, but dot notation also works for convenience in many cases.

Example:

JSON

```sql
select request_parameters->>'optional_field'
from aws_cloudtrail_log;

-- Access a field that may or may not exist
```

STRUCT

```sql
select user_identity.optional_field
from aws_cloudtrail_log;

-- Structs require the field to exist in the schema
-- This query fails if 'optional_field' is missing from the struct
```


