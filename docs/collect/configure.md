---
title: Configure Collection

---

# Configure Collection

Before you can begin collecting logs, you must tell Tailpipe what to collect.  Tailpipe configuration is defined using HCL in one or more Tailpipe config (`.tpc`) files in the config directory (`~/.tailpipe/config` by default).  


## Tables
Ultimately, the data that Tailpipe collects ends up in tables that you can query with SQL.

Tailpipe [plugins](/docs/collect/plugins) define tables for common log sources and formats.  You don't need to define these tables; simply create one or more [partition](/docs/collect/configure#partitions) for the table and begin [collecting logs](/docs/collect/collect)!  

If your logs are not in a standard format or are not currently supported by a plugin, you can create [custom tables](/docs/collect/custom-tables) to collect data from arbitrary log files and other sources.

Tables are implemented as DuckDB views over the Parquet files.  Tailpipe creates tables (that is, creates views in the `tailpipe.db` database) based on the data and metadata that it discovers in the [workspace](#workspaces), along with the filter rules.

When you run `tailpipe query` or `tailpipe connect`, Tailpipe finds all the tables in the workspace according to the [hive directory layout](/docs/collect/configure#hive-partitioning) and adds a view for the table.  The view definitions will include qualifiers that implement any filter arguments that you specify (`--from`,`--to`,`--index`,`--partition`).

You can see what tables are available with the `tailpipe plugin list` command. 

## Partitions
A partition represents data gathered from a [source](/docs/collect/configure#sources). Partitions are defined [in HCL](/docs/reference/config-files/partition) and are required for [collection](/docs/collect/collect).  

```hcl
partition "aws_cloudtrail_log" "test" {

  source "aws_s3" {
    connection = connection.aws.logs
    bucket     = "my-logs-bucket"
  }
  
}
``` 

The partition has two labels:

1. The [table](#tables) name. The table name is meaningful and must match a table name for an installed [plugin](/docs/collect/plugins) or a [custom table](/docs/collect/custom-tables). 
2. A partition name.  The partition name must be unique for all partitions in a given table (though different tables may use the same partition names).  

The partition must also contain a [`source` block](#sources) that defines the location of the source log files as well as the [`connection`](#connections) information to interact with it.

The data for each table partition will be stored in its own subdirectory in the [hive](#hive-partitioning).

At query time, Tailpipe discovers partitions in the [workspace](/docs/reference/config-files/workspace) and automatically creates tables based on the partitions it finds.  For instance, if you define three `aws_cloudtrail_log` partitions, the `aws_cloudtrail_log` table will include the data from all three.


### Hive partitioning

Tailpipe uses [hive partitioning](https://duckdb.org/docs/data/partitioning/hive_partitioning.html) to leverage automatic [filter pushdown](https://duckdb.org/docs/data/partitioning/hive_partitioning.html#filter-pushdown) and Tailpipe is opinionated on the layout:

  - The data is written to Parquet files in the workspace directory, with a prescribed directory and filename structure.  Each partition is written to a separate directory.

  - The `tp_index` is used to partition the data and defaults to `"default"` if not specified. You can configure the `tp_index` in your partition config to specify a column whose value should be used as tp_index. Be aware that defining a `tp_index` does not always increase performance and may, in fact, decrease it as it can result in many small parquet files.

The standard partitioning/hive structure enables efficient queries that only need to read subsets of the hive filtered by index or date.  Because the data is laid out into partitions,  performance is optimized when the partition appears in a `where` or `join` clause.  The index provides a way to segment the data to optimize lookup performance in a way that is *optimal for your specific use case*.  For example, you might index on account ID for AWS tables, subscription for Azure tables, or project ID for GCP tables. 

```bash
tp_table=aws_cloudtrail_log
└── tp_partition=prod
    └── tp_index=default
        ├── tp_date=2024-12-31
        │   └── data_20250106140713_740378_0.parquet
        ├── tp_date=2025-01-01
        │   └── data_20250106140713_740378_0.parquet
        ├── tp_date=2025-01-02
        │   └── snap_20250106140823_952067.parquet
        ├── tp_date=2025-01-03
        │   └── snap_20250106140824_011599.parquet
        ├── tp_date=2025-01-04
        │   └── data_20250106140752_829722_0.parquet
        ├── tp_date=2025-01-05
        │   └── snap_20250106140824_073116.parquet
        └── tp_date=2025-01-06
            └── snap_20250106140824_131637.parquet
```



## Sources

A partition acquires data from a [source](/docs/reference/config-files/partition#source).  Often, a source will connect to a resource via a [connection](#connections), which specifies the credentials and account scope.

```hcl
partition "aws_cloudtrail_log" "test" {
  source "aws_s3_bucket" {
    connection = connection.aws.logs
    bucket     = "my-logs-bucket"
  }

}
```

The block label denotes the source type - `aws_s3_bucket`, `file`, etc. The source types are defined in plugins, and the arguments vary by type.  The[ Tailpipe Hub](https://hub.tailpipe.io) provides extended documentation and examples for plugin sources. The [`file` source](/docs/reference/config-files/partition#file-source) is provided by the core plugin, which is included in every Tailpipe installation.

The source is responsible for:
- turning raw data into rows
- initiating file transfers or requests
- downloading or copying raw data
- unzipping/untaring, etc
- incremental transfer, tracking, and retrying

<!--
- source-specific filtering for sources that support them, e.g. [Cloudwatch log filters](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_FilterLogEvents.html)


-->

## Connections

**[Connections](/docs/reference/config-files/connection)** provide *credentials* and *configuration options* to connect to external services.  Tailpipe connections are similar to connections in Steampipe and Flowpipe.

```hcl
connection "aws" "aws_01" {
  profile = "aws_01"
}

connection "aws" "aws_03" {
  access_key = "AKIA4YFAKEKEYXTDS252"
  secret_key = "SH42YMW5p3EThisIsNotRealzTiEUwXN8BOIOF5J8m"
}

connection "gcp" "gcp_my_other_project" {
  project     = "my-other-project"
  credentials = "/home/me/my-service-account-creds.json"
}
```

Connection types are defined in plugins. Each type creates a [default connection](/docs/reference/config-files/connection#default-connections) (e.g., `connection.aws.default`), which can be overridden in a `.tpc` file. Each plugin has its own default credential resolution.
