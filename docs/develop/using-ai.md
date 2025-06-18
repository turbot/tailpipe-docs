---
title: Using AI
sidebar_label: Using AI
---

# Using AI

Creating new tables for Tailpipe plugins with AI tools and IDEs works remarkably well. At Turbot, we develop plugin tables frequently and use AI for almost every new table we create. We've experimented with various approaches, including detailed prompt engineering, explicit guidelines, IDE rules and instructions, and complex workflows, but found that AI typically produces excellent results even without heavy guidance.

The key to this success is working within existing plugin repositories and opening the entire repository as a folder or project in your IDE. This gives AI tools access to existing table implementations, documentation examples, code patterns, and naming conventions to generate consistent, high-quality results without extensive prompting.

If you're looking to use AI to query Tailpipe rather than develop new tables, you can use the [Tailpipe MCP server](https://github.com/turbot/tailpipe-mcp), which provides powerful tools for AI agents to inspect tables and run queries.

## Getting Started

While AI often works well with simple requests like "Create a table for [log_type]", here are some prompts we use at Turbot that you may find helpful as starting points.

### Prerequisites

1. Open the plugin repository in your IDE (Cursor, VS Code, Windsurf, etc.) to give AI tools access to all existing code and documentation.
2. Ensure you have Tailpipe installed (`brew install turbot/tap/tailpipe` for MacOS or the installation script for Linux/WSL).
3. Set up access credentials for the cloud provider (e.g., AWS credentials).
4. Configure test log sources (e.g., S3 buckets with sample logs, CloudWatch Log Groups).
5. Configure the [Tailpipe MCP server](https://github.com/turbot/tailpipe-mcp) which allows the agent to inspect tables and run queries.

### Create Table

First, create the new table and its documentation, using existing tables and docs as reference.

```md
Your goal is to create a new Tailpipe table and documentation for <log_type>.

1. Review existing tables and their documentation in the plugin to understand:
   - Table structure patterns
   - Source configurations (S3, CloudWatch, etc.)
   - Standard field enrichment
   - Naming conventions

2. Implement the table with:
   - Proper source metadata configuration
   - Row enrichment logic
   - Standard fields (tp_id, tp_timestamp, etc.)
   - Log-specific fields
   - Extractor implementation for parsing logs

3. Create documentation including:
   - Table overview and description
   - Configuration examples for each source type
   - Example queries for common use cases
   - Schema reference
```

### Build Plugin

Next, build the plugin with your changes and verify your new table is properly registered.

```md
Your goal is to build the plugin and verify that your new <log_type> table is properly registered and functional.

1. Build the plugin:
   ```sh
   make
   ```

2. Verify the table is registered:
   ```sh
   tailpipe plugin list
   ```

3. Check the table schema:
   ```sh
   tailpipe query
   > .inspect aws_<log_type>
   ```
```

### Configure Log Sources

```md
Your goal is to configure log sources for <log_type> to validate your Tailpipe table implementation.

1. Create configuration in ~/.tailpipe/config/aws.tpc with appropriate source type:

   For S3:
   ```hcl
   partition "aws_<log_type>" "s3_logs" {
     source "aws_s3_bucket" {
       connection = connection.aws.test_account
       bucket     = "test-logs-bucket"
     }
   }
   ```

   For CloudWatch:
   ```hcl
   partition "aws_<log_type>" "cloudwatch_logs" {
     source "aws_cloudwatch_log_group" {
       connection = connection.aws.test_account
       log_group_name = "/aws/my-log-group"
     }
   }
   ```

2. Ensure sample logs are available in your configured source
```

### Validate Data Collection

Next, collect and query the logs to test that the table implementation works correctly.

```md
Your goal is to thoroughly test your <log_type> table implementation by validating data collection and querying.

1. Collect logs from the configured source:
   ```sh
   tailpipe collect aws_<log_type>
   ```

2. Validate data collection:
   - Check collection status and statistics
   - Verify log parsing and enrichment
   - Confirm partition organization

3. Test queries:
   ```sh
   tailpipe query
   ```
   - Execute each example query from documentation
   - Verify field types and values
   - Test filtering and aggregation
   - Validate enriched fields

4. Document test results:
   - Collection statistics
   - Query results
   - Any parsing or enrichment issues
```

### Cleanup Test Resources

After testing is completed, clean up test resources and data.

```md
Your goal is to clean up all test resources and data created for <log_type> validation.

1. Remove test data:
   - Delete test log files from S3
   - Clean up test log streams from CloudWatch
   - Remove any other test artifacts

2. Verify cleanup:
   - Check source locations are clean
   - Confirm all test resources are removed
   - Document any persistent resources that should be retained
```
