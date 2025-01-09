---
title: Querying JSON
---

# Querying JSON Columns

Logs can contain complex data represented as JSON. Tailpipe plugins store such objects as one of two native DuckDB types: JSON or STRUCT. Learn about JSON idioms here, see [Querying STRUCT Columns](/docs/sql/querying-struct) for STRUCT idioms.

When an object's instances have irregular shape, a plugin uses DuckDB's JSON type. The `request_parameters` column of the `aws_cloudtrail_log` table is a JSON column, as you can verify using the `typeof` function.

```sql
select typeof(request_parameters) from aws_cloudtrail_log limit 1;
JSON
```

You can list the keys of a JSON object:

```sql
select json_keys(request_parameters) from aws_cloudtrail_log limit 1;
```

The request_parameters` column contains a JSON object that includes a `Host` key whose value you can extract with a function:

```sql
select json_extract(request_parameters, '$.Host') from aws_cloudtrail_log;
```

Or with the JSON operator that returns a stringified representation of an element:

```sql
select request_parameters ->> 'Host' from aws_cloudtrail_log;
```

Both methods return a string that you can compare.

```sql
select json_extract(request_parameters, '$.Host') ilike '%aws%' from aws_cloudtrail_log;

select ( request_parameters ->> 'Host' ) ilike '%aws%' from aws_cloudtrail_log;
```

In this case, DuckDB's precedence rules require you to parenthesize the `->>` expression. To avoid confusion we prefer functions over operators in Tailpipe mods.


The `resource` column of `aws_cloudtrail_log` is a JSON array of objects. You use 0-based indexing to access elements of an array. To access the first element:

```sql
select resources[0] from aws_cloudtrail_log;
```

Alternatively you can use the JSON `->` operator:

```sql
select resources -> 0 from aws_cloudtrail_log;
```

Either approach returns a JSON object that you can drill into.

While this is also possible:

```sql
select resources ->> 0 from aws_cloudtrail_log;
```

it's probably not you want because `->>` returns a string, not a JSON object.

To access an element of the zeroth object in the array:

```sql
select json_extract(resources[0], '$.ARN') from aws_cloudtrail_log;
```

Or:

```sql
select resources -> 0 ->> 'ARN' from aws_cloudtrail_log;
```

Either method returns a string suitable for comparison with another string, or evaluation by a string-oriented operator such as `ilike`.

While this is also possible:

```sql
select resources -> 0 -> 'ARN' from aws_cloudtrail_log;
```

The JSON object it returns has type JSON, not string, so this comparison fails:

```sql
select resources -> 0 -> 'ARN' ilike '%aws%' from aws_cloudtrail_log;
```

You can parenthesize to enable the comparison:

```sql
select ( resources -> 0 -> 'ARN' ) ilike '%aws%' from aws_cloudtrail_log;
```

But again, to avoid confusion, you may want to prefer JSON functions over operators.

