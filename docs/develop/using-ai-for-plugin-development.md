---
title: Using AI for Plugin Development
sidebar_label: Using AI for Plugin Development
---

# Using AI for Plugin Development

Creating new tables for Tailpipe plugins with AI tools and IDEs works remarkably well. At Turbot, we develop plugin tables frequently and use AI for almost every new table we create. We've experimented with various approaches, including detailed prompt engineering, explicit guidelines, IDE rules and instructions, and complex workflows, but found that AI typically produces excellent results even without heavy guidance.

The key to this success is working within existing plugin repositories and opening the entire repository as a folder or project in your IDE. This gives AI tools access to existing table implementations, documentation examples, code patterns, and naming conventions to generate consistent, high-quality results without extensive prompting.

If you're looking to use AI to query Tailpipe rather than develop new tables, you can use the [Tailpipe MCP server](https://github.com/turbot/tailpipe-mcp), which provides powerful tools for AI agents to inspect tables and run queries.

## Getting Started

While AI often works well with simple requests like "Create a table for [log_type]", here are some prompts we use at Turbot that you may find helpful as starting points.

### Prerequisites

1. Open the plugin repository in your IDE (Cursor, VS Code, Windsurf, etc.) to give AI tools access to all existing code and documentation.
2. Ensure you have Tailpipe installed with a connection and partition configured for the plugin.
3. Set up access to create test resources in the provider.
4. Configure the [Tailpipe MCP server](https://github.com/turbot/tailpipe-mcp) which allows the agent to inspect tables and run queries.

### Create Table

First, create the new table and its documentation, using existing tables and docs as reference.

#### Prompt

```md
Your goal is to create a new Tailpipe table and documentation for <log type>.

1. Review existing tables and their documentation in the plugin to understand the established patterns, naming conventions, and column structures.

2. Create the table implementation with proper source metadata configuration, row enrichment logic for standard and log-specific fields.

3. Register the new table in plugin.go in alphabetical order.

4. Create documentation at `docs/tables/<table_name>/index.md` and `docs/tables/<table_name>/queries.md`.
  - For DuckDB queries:
    - Use the `.` operator to access struct fields (e.g., `table.struct_field`) instead of `->` or `->>`.
    - Use the `->` and `->>` operators to access JSON fields (e.g., `table ->> 'json_field'`) instead of `.` or `json_extract()`.
    - When using `->` and `->>` operators to access JSON fields in `where` clauses, wrap the code in parenthesis, e.g., `where (table ->> 'json_field') = '...'`.
  - Include example queries with activity, detection, operational, and volume scenarios.
```

### Build Plugin

Next, build the plugin with your changes and verify your new table is properly registered.

#### Prompt

```
Your goal is to build the plugin using the exact commands below and verify that your new <log_type> table is properly registered and functional.

1. Build the plugin using the `make` command.

2. Verify the plugin built correctly by running the `tailpipe plugin list` tool.

3. Test if the Tailpipe MCP server is available by running the `tailpipe_table_list` tool.

4. If the MCP server is available, use `tailpipe_table_show` to verify the table exists in the schema and can be queried successfully.

5. If the MCP server is not available, verify table registration manually with `tailpipe query "select column_name, data_type from information_schema.columns where table_name = '<table_name>' order by ordinal_position"`, then test basic querying with `tailpipe query "select * from <table_name>"`.
```

### Create Test Logs

To test the table's functionality, you'll need log data to query. You can either use existing logs or create new resources to generate logs. 

#### Prompt

```
Your goal is to create test log data for <log_type> that will be stored in <source_type> to validate your table implementation.

1. Create test resources that will generate the logs along with the log destination resources.
  - Use the provider's CLI if available, Terraform configuration if CLI isn't available, or API calls via shell script as a last resort.
  - Create any dependent resources needed.
  - Use the most cost-effective configuration. If the estimated cost is high, e.g., $50, warn about the expense rather than proceeding.

2. Verify that all resources were created successfully using the same tool or method used for creation.

3. Execute operations that will generate both successful and error logs.

4. Wait for the logs to be generated (this depends on the log delivery time).

5. Verify that logs were created successfully (using the same tool or method used for creation if possible).
```

### Collect Logs

After the logs are generated, you can now collect them. You can collect the logs using the agent, but we recommend running the `tailpipe collect` command in a separate terminal, as collection can take a while.

Please see [tailpipe collect](https://tailpipe.io/docs/reference/cli/collect) for examples.

### Validate Column Data

Next, query the table to test that columns and data types are correctly implemented.

#### Prompt

```md
Your goal is to thoroughly test your <log_type> table implementation by validating column data and executing documentation examples.

1. Execute `select * from <table_name>` to validate that all columns return expected data based on the actual log entries and have correct data types.

2. Test each example query from the table documentation to verify the SQL syntax is correct, queries execute without errors, and results match the example descriptions.

3. Share all test results in raw Markdown format to make them easy to export and review.
```

### Cleanup Test Resources

After testing is completed, remove any resources created for testing. You can optionally keep the log destination resources for future collection and testing.

#### Prompt

```md
Your goal is to clean up all test resources created for <log_type> validation to avoid ongoing costs.

1. Delete all resources created for testing, including any dependent resources, using the same method that was used to create them.

2. Verify that all resources were successfully deleted, using the same method that was used to delete them.
```
