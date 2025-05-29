---
title: Implementing Tables
sidebar_label: Implementing Tables
---

# Implementing Tables

> [!TIP]
> If your logs are obtainable from an existing source, it may be worth experimenting with a [Custom Table](/docs/collect/custom-tables) before developing a code-based solution as this will allow for faster iteration when testing. 

When developing a plugin, you'll need to implement tables to define the structure of your log data. Tailpipe provides a flexible framework for implementing different types of tables based on your data source and format requirements.

## Types of Plugin Tables

Plugin tables can be categorized based on how they handle incoming data formats:

### Static Tables

Static tables are designed for data sources with a fixed, well-defined structure, such as API responses or structured data formats. These tables expect the data to always follow a consistent format, typically defined as a Go struct.

Key characteristics of static tables:
- Fixed schema defined by a Go struct
- Direct mapping from source data to table columns
- Optimized for structured data sources like API responses
- Minimal parsing overhead as the structure is known in advance

Examples of static tables include:
- Audit log tables in `tailpipe-plugin-pipes`, `tailpipe-plugin-github`, and `tailpipe-plugin-gcp`

### Pre-defined Custom Tables

Pre-defined custom tables are designed for data sources with variable formats but a known set of possible fields. These tables define a superset of all possible columns, but only use a subset based on the specific format of the incoming data.

Key characteristics of pre-defined custom tables:
- Comprehensive schema defining all possible columns
- Format-specific parsing logic to extract relevant fields
- Support for multiple format variations of the same data type
- Flexible mapping from source data to table columns

Examples of pre-defined custom tables include:
- Access log tables in `tailpipe-plugin-nginx` and `tailpipe-plugin-apache`

## Implementing a Static Table

To implement a static table, you'll need to:

1. **Define a struct that represents your table's row data**: This struct should include all the fields that will be columns in your table. It should also embed `schema.CommonFields` to include the standard fields required by Tailpipe.

2. **Implement the table interface**: Create a struct for your table and implement methods like:
   - `Identifier()`: Returns a unique identifier for your table
   - `GetSourceMetadata()`: Defines the sources from which data can be collected and the mappers to use
   - `EnrichRow()`: Processes each row, adding standard fields and extracting relevant information

3. **Create a mapper**: Implement a mapper that transforms source data into your table's row struct. The mapper should handle the source input type and map to your schema struct.
   - If multiple sources are supported, you can create multiple mappers for different source types.

4. **Register your table**: In your plugin's main package (typically in a file like `plugin.go`), register your table using the `RegisterTable` function:

   ```
   table.RegisterTable[*audit_log.AuditLog, *audit_log.AuditLogTable]()
   ```

   This makes your table available to Tailpipe.

For a real-world example, see the audit log table implementation in the Pipes plugin:
- [audit_log.go](https://github.com/turbot/tailpipe-plugin-pipes/blob/main/tables/audit_log/audit_log.go)
- [audit_log_table.go](https://github.com/turbot/tailpipe-plugin-pipes/blob/main/tables/audit_log/audit_log_table.go)
- [audit_log_mapper.go](https://github.com/turbot/tailpipe-plugin-pipes/blob/main/tables/audit_log/audit_log_mapper.go)

## Implementing a Pre-defined Custom Table

To implement a pre-defined custom table, you'll need to:

1. **Define a table struct that embeds `CustomTableImpl`**: This provides the base implementation for custom tables.

2. **Define a comprehensive table schema**: Use `GetTableDefinition()` to define all possible columns that could appear in your table, even if not all will be used in every instance.

3. **Implement format-specific parsing logic**: Create a format implementation that can parse your log format into the table schema. This might involve regex patterns or other parsing techniques.

4. **Ensure you map fields via EnrichRow**: The `EnrichRow` function on your table should provide mapping logic, although mapping of format matches to columns can primarily be done by returning `return c.CustomTableImpl.EnrichRow(row, sourceEnrichmentFields)`.

5. **Register your table and formatters**: In your plugin's main package (typically in a file like `plugin.go`), register your table and formatters:

   ```
   // Register the table
   table.RegisterCustomTable[*access_log.AccessLogTable]()

   // Register the format type
   table.RegisterFormat[*access_log.AccessLogTableFormat]()

   // Register any format presets
   table.RegisterFormatPresets(access_log.AccessLogTableFormatPresets...)
   ```

   This makes your table and formatters available to Tailpipe.

For a real-world example, see the access log table implementation in the Nginx plugin:
- [access_log_table.go](https://github.com/turbot/tailpipe-plugin-nginx/blob/main/tables/access_log/access_log_table.go)
- [access_log_table_format.go](https://github.com/turbot/tailpipe-plugin-nginx/blob/main/tables/access_log/access_log_table_format.go)
- [access_log_table_format_presets.go](https://github.com/turbot/tailpipe-plugin-nginx/blob/main/tables/access_log/access_log_table_format_presets.go)

## Best Practices

When implementing tables in your plugin, consider the following best practices:

1. **Choose the right table type**: Use static tables for fixed-format data and pre-defined custom tables for variable-format data (where a superset of columns is known).

2. **Provide comprehensive documentation**: Document all columns, their data types, and their meanings.

3. **Handle edge cases**: Ensure your table implementation can handle missing or malformed data gracefully.

4. **Optimize for performance**: Consider the performance implications of your parsing and mapping logic, especially for large volumes of data.

5. **Test with real data**: Test your table implementation with real-world data to ensure it works correctly in all scenarios.
