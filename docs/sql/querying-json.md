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
- Tailpipe plugins produce JSON columns for regularly-shaped data.

## Key Operators and Methods

### For JSON Columns
1. **Accessing Values**:
   - `->`: Extracts a field as JSON. Use this when you want to retain the JSON type.
   - `->>`: Extracts a field as a plain string. Use this when you need a direct value for comparisons or display.
   - `json_extract` or `json_extract_string`: Use these for more complex extractions, including nested paths or computed keys.

2. **Comparisons**:
   - Always use `->>` or `json_extract_string` for comparisons with plain strings because `->` returns a JSON object that cannot be directly compared to strings.

   Example:
   ```sql
   -- This works
   SELECT COUNT(*)
   FROM aws_cloudtrail_log
   WHERE request_parameters->>'Host' = 'example.com';
   ```

   ```sql
   -- This fails
   SELECT COUNT(*)
   FROM aws_cloudtrail_log
   WHERE request_parameters->'Host' = 'example.com';
   ```

3. **Dot Notation**:
   - For JSON columns, Tailpipe will allow an expression like `request_parameters.Host`, it generally behaves like `->` (returns JSON) and requires an explicit conversion for string comparisons.


### For Struct Columns
1. **Accessing Values**:
   - Dot notation (`.key`) is the default and most intuitive way to access fields within a struct.
   - Dot notation directly extracts the value in its native type (e.g., string, number).

Example:

```sql
select typeof(user_identity) from aws_cloudtrail_log limit 1;
STRUCT("type" VARCHAR, principal_id VARCHAR, arn VARCHAR,
account_id VARCHAR, access_key_id VARCHAR, ...
```
   
```
select count(user_identity.invoked_by) from aws_cloudtrail_log 
where user_identity->>'invoked_by' = 'AWS Internal';
```

2. **Comparisons**:
   - Struct fields are directly comparable without additional casting.
   - Dot notation simplifies field access for both selection and comparison.

3. **JSON-Like Operators**:
   - Applicable to struct fields in DuckDB. You can use `->` to extract a field as its native type or `->>` to extract it as a plain string, just like with JSON columns. Structs, however, enforce stricter typing than JSON.

Example:
```sql
select count(user_identity->>'invoked_by') as invoked_by_count
from aws_cloudtrail_log
where user_identity->>'invoked_by' = 'AWS Internal';
```   


## Comparison: JSON vs Structs

| Aspect                      | JSON (e.g., `request_parameters`)     | Struct (e.g., `some_struct_field`)       |
|-----------------------------|---------------------------------------|------------------------------------------|
| **Field Access**            | `->` (JSON) or `->>` (string)         | `.key`                                   |
| **Field Extraction Type**   | JSON (`->`), String (`->>`)           | Native type of the field                 |
| **Comparison Readiness**    | Use `->>` or `json_extract_string`    | Direct comparison                        |
| **Complexity**              | Flexible, but needs explicit handling | Simpler, as types are already defined    |


## Recommendations

1. **Use JSON for Flexibility**:
   - If your data structure changes frequently, or you expect varying schemas, JSON is more adaptable.
   - Use `->` for JSON processing and `->>` when plain string values are required (e.g., for comparisons).

```sql
select request_parameters->>'Host' AS host
from aws_cloudtrail_log
where request_parameters->>'Host' = 'example.com';
   ```

2. **Use Structs for Consistency**:
  - For well-defined schemas with fixed field names and types, structs are preferable.
  - Access with dot notation for cleaner, more concise syntax.

```sql
select some_struct_field.key
from table_with_struct
where some_struct_field.key = 'example.com';
```

3. **Avoid Mixing Notations**:
- For JSON columns, avoid relying on dot notation like `request_parameters.Host`, as it may cause type issues or confusion.
- Instead, explicitly use JSON operators (`->`, `->>`) or functions.

4. **Use `json_extract_string` for More Control**:
- For deeply nested JSON, prefer `json_extract_string` for clarity and precision.
- Example:
```sql
select json_extract_string(request_parameters, 'nested.key')
from aws_cloudtrail_log;
```

