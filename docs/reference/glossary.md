---
title: Glossary
---

# Glossary

## Collection

A collection is a tree of Parquet files in the standard hive format. It is created by a plugin when you run the `tailpipe collect` command. 

## Compaction

A collection may result in storage of multiple Parquet files for a given day. Tailpipe can compact those into a single file for the day. This happens automatically when you run `tailpipe collect`.

## Benchmark

A benchmark bundles a set of detections and/or sub-benchmarks into a single unit.

## Common Columns

In addition to populating a log-specific schema, a Tailpipe plugin can (where appropriate) map log-specific fields to common columns like `TpDate`, `TpIps`, or `TpUsernames`. That enables joins across multiple Tailpipe tables, so you can, for example, write a single query to look for a known malicious IP address in logs from different sources.

## Connection

A connection represents a reusable connection to a service. In a `.tpc` file, the `connection` block specifies credentials (e.g. an AWS profile or access key) and other information (e.g. AWS region).

## Detection

A detection is a Tailpipe query, optionally bundled into a benchmark, that runs in the context of a mod. The detection's query looks for a specific pattern. In a Cloudtrail log, for example, an `UpdateTrail` event with a null `KmsKeyId` in its request parameters indicates the removal of a KMS key. The AWS detections mod includes `cloudtrail_logs_detect_cloudtrail_trails_with_encryption_disabled`, a detection that queries for this pattern and associates it with MITRE ATT&CK `TA0005:T1562.001`.

## DuckDB

Tailpipe uses DuckDB, an embeddable column-oriented database. DuckDB reads the Parquet files created by `tailpipe collect` and enables queries against that data.

## Hive

A tree of Parquet files in the Tailpipe workspace (by default,`~/.tailpipe/data/default`). The `tailpipe.db` in `~/.tailpipe/data/default` (and derivatives created by `tailpipe connect`, e.g. `tailpipe_20241212152506.db`) are thin wrappers that materialize views over the Parquet data.

## Index

A partition may be subdivided by one or more plugin-defined indexes. A partition of an AWS table, for example, will be indexed by one or more AWS account IDs. You can use the index to filter a query, e.g. `select count(*) from aws_cloudtrail_log where account_id = '123456789'`.  

## Mod

A Tailpipe mod provides the context for detections (usually organized into benchmarks) and/or dashboards.

## Source

A plugin-defined mechanism for acquiring log data. When you configure a collection in a `.tpc` file, you use the `source` argument. Example values include `file` and `aws_s3_bucket`, which are generic ways to acquire log files (enhanced by plugin-specific configuration). A source like [pipes_audit_log_api](https://hub-tailpipe.io/plugins/turbot/pipes/sources/pipes_audit_log_api) is an example of a plugin-specific mechanism to acquire log data via an API.

## Parquet

Parquet is a columnar storage file format that is optimized for reading and writing large datasets. It is the backing file format of the logs collected via Tailpipe and exposed as tables you query with SQL.

## Partition

A partition is a subset of a table. The `partition` block in a `.tpc` file defines a named partition and includes a `source` block used to populate the partition. You can use the partition name to filter a query, e.g. `select count(*) from aws_cloudtrail_log where partition = 'prod'`.

## Table

Every Tailpipe query refers to one more tables built by the collection process.

