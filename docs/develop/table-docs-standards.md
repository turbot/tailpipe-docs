---
title: Table Documentation Standards
sidebar_label: Table Documentation Standards
---

# Table Documentation Standards

## Overview

Creating table documentation is an important part of developing tables for Tailpipe. Each table should have comprehensive documentation that helps users understand the table's purpose, how to configure and collect data, and how to query the data effectively.

In Tailpipe, table documentation is split into two files within a table-specific directory:

1. `index.md` - Contains the table description, configuration instructions, collection instructions, and basic example queries
2. `queries.md` - Contains a comprehensive set of example queries organized by category

For example, for a table named `aws_s3_bucket`, the documentation would be in:
- `docs/tables/aws_s3_bucket/index.md`
- `docs/tables/aws_s3_bucket/queries.md`

## Documentation Structure

Each table should have its own directory under `docs/tables/` with the following structure:

```
docs/tables/
└── table_name/
    ├── index.md
    └── queries.md
```

## index.md Guidelines

The `index.md` file should include the following sections:

1. Front matter
2. Table Description
3. Configuration Section
4. Collection Section
5. Query Section
6. Example Configurations Section
7. Source Defaults Section (if applicable)

### Front matter

The front matter should include the title and description of the table:

```
---
title: "Tailpipe Table: table_name - Query [resource description]"
description: "Brief description of the table and the data it provides."
---
```

Example:
```
---
title: "Tailpipe Table: aws_s3_bucket - Query AWS S3 Buckets"
description: "AWS S3 buckets are cloud storage resources that provide scalable, durable, and secure object storage in Amazon Web Services."
---
```

### Table Description

The table description should start with a header and provide a brief overview of the table and the data it provides:

```
# Table: table_name - Query [resource description]

The `table_name` table allows you to query [resource description with link to provider documentation]. This table provides [brief description of the data and its uses].
```

Example:
```
# Table: aws_s3_bucket - Query AWS S3 Buckets

The `aws_s3_bucket` table allows you to query [AWS S3 buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html). This table provides detailed information about your S3 buckets, including configuration, permissions, and metadata.
```

### Configuration Section

The configuration section should explain how to create a partition for the table:

```
## Configure

Create a [partition](https://tailpipe.io/docs/manage/partition) for `table_name` ([examples](#example-configurations)):

```sh
vi ~/.tailpipe/config/provider.tpc
```

```hcl
connection "provider" "account_name" {
  // connection details
}

partition "table_name" "partition_name" {
  source "source_name" {
    connection = connection.provider.account_name
    // source configuration
  }
}
```

### Collection Section

The collection section should explain how to collect data for the table:

```
## Collect

[Collect](https://tailpipe.io/docs/manage/collection) logs for all `table_name` partitions:

```sh
tailpipe collect table_name
```

Or for a single partition:

```sh
tailpipe collect table_name.partition_name
```

### Query Section

The query section should include a link to the queries page and a few basic example queries:

```
## Query

**[Explore X+ example queries for this table →](https://hub.tailpipe.io/plugins/turbot/provider/queries/table_name)**

### Basic Query Example

Brief description of the query.

```
select
  column1,
  column2,
  column3
from
  table_name
where
  condition
order by
  column1;
```

### Another Query Example

Brief description of the query.

```
select
  column1,
  count(*) as count
from
  table_name
group by
  column1
order by
  count desc;
```
```

### Example Configurations Section

The example configurations section should provide various configuration examples for different scenarios:

```
## Example Configurations

### Basic Configuration

Brief description of the configuration.

```hcl
partition "table_name" "partition_name" {
  source "source_name" {
    // source configuration
  }
}
```

### Configuration with Filtering

Brief description of the configuration with filtering.

```hcl
partition "table_name" "partition_name" {
  filter = "column != value"

  source "source_name" {
    // source configuration
  }
}
```

### Source Defaults Section

If the table has default source configurations, they should be documented in this section:

```
## Source Defaults

### source_name

This table sets the following defaults for the [source_name source](https://hub.tailpipe.io/plugins/turbot/provider/sources/source_name#arguments):

| Argument    | Default |
|-------------|---------|
| argument1   | default_value1 |
| argument2   | default_value2 |
```

## queries.md Guidelines

The `queries.md` file should contain a comprehensive set of example queries organized by category. Each category should have multiple example queries that demonstrate different aspects of the table.

### Query Categories

Queries should be organized into categories based on their purpose. Common categories include:

1. **Activity Examples** - Queries that show general usage patterns and trends
2. **Detection Examples** - Queries that help identify potential issues or security concerns
3. **Operational Examples** - Queries that help with troubleshooting and performance monitoring
4. **Volume Examples** - Queries that analyze traffic patterns and data transfer

### Query Format

Each query should include:

1. A descriptive title as a level 3 heading (###)
2. A brief description explaining the purpose and benefits of the query
3. SQL code formatted in a code block
4. A YAML block with a "folder" property for categorization

Example:

```
### Daily Request Trends

Count requests per day to identify traffic patterns over time.

```
select
  strftime(timestamp, '%Y-%m-%d') as request_date,
  count(*) as request_count
from
  table_name
group by
  request_date
order by
  request_date asc;
```

```yaml
folder: Category
```

### Query Style Conventions

- Use H3 (`###`) for query titles
- Format SQL queries consistently:
  - Use lowercase for SQL keywords
  - Use 2 spaces for indentation
  - Include spaces after commas
  - Align related clauses
- Query titles should be in the imperative mood (e.g., "List resources", "Count events")
- Query titles should use the plural form of the resource name if the query can return more than one row
- Query descriptions should be concise but informative, explaining the purpose and benefits of the query
- All queries must be DuckDB compliant

## DuckDB Compliance

All example queries in Tailpipe documentation **MUST** be DuckDB compliant. This means:

1. Using DuckDB-compatible functions and syntax
2. Avoiding functions or features that are specific to other database systems
3. Testing queries to ensure they work with DuckDB

Common DuckDB-specific syntax to be aware of:

- Use `strftime()` for date formatting instead of database-specific functions
- Use `date_trunc()` for truncating dates to a specific precision
- Use standard SQL aggregation functions (count, sum, avg, etc.)
- Use `regexp_matches()` for regular expression matching
- Avoid using database-specific extensions or custom functions

For more information on DuckDB SQL syntax, refer to the [DuckDB SQL documentation](https://duckdb.org/docs/sql/introduction).
