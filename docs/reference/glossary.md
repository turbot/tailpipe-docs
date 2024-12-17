---
title: Glossary
---

>[!NOTE]
> I have now moved this under reference to match other sites.

>[!NOTE]
> This glossary from graza initially covered dev-oriented advanced topics. We don't have an analog for that elsewhere. It's counterpart in Guardrails, also under reference/docs, is conceptual overview for users not devs. Maybe this should serve both audiences. We don't have prior art for that, but here's a sketch.

# Glossary

## Basic topics for everyone

### Collection

A collection is the set of per-day parquet files created by a plugin when you run the `tailpipe collect` command. 

### Connection

A connection is a HCL configuration item that represents a reusable connection to a service, i.e. AWS access keys, region, etc.

### DuckDB

Tailpipe uses DuckDB, an embeddable column-oriented database.

### Hive

A tree of parquet files in `~/.tailpipe/data/default` rooted at, e.g., `tp_table=aws_cloudtrail_log`. The `tailpipe.db` in `~/.tailpipe/data/default` (and derivatives created by `tailpipe connect`, e.g. `tailpipe_20241212152506.db`), are thin wrappers that materialize views over the parquet data.

### Source

>[!NOTE]
> basic discussion

`source` is an argument that appears in .tpc files. Example values: `source "aws_s3_bucket"`, `source "file_system"`, `source "pipes_audit_log_api"`.

### Parquet

Parquet is a columnar storage file format that is optimized for reading and writing large datasets. It is the backing file format of the logs collected via Tailpipe, these are exposed by DuckDB.

### Partition

A partition is a HCL configuration item (e.g. `partition "aws_cloudtrail_log" "prod") that represents an instance of a table with provided table and source configurations. They are defined in  `.tpc` configuration files in the Tailpipe installation's config directory (e.g. `~/.tailpipe/config`).

### Table

Every Tailpipe query refers to one more tables built by the collection process (e.g. `select count(*) from aws_cloudtrail_log`).

## Advanced topics for developers

### Enrichment

Enrichment is the process of populating additional fields of a log entry, in Tailpipe this refers to the common `tp_*` fields.

### Mapper

Mappers reside in the `mappers` package of the plugin, they take in data from a source and return a collection of the associated row struct.

### Row Struct

A row struct is a codified representation of a table schema, these reside in the `rows` package of the plugin. 

Incorporating required fields with `json` tags to define column namings and optionality, including the common fields for enrichment by embedding the `enrichment.CommonFields` struct.

### Source

Sources reside in the `sources` package of the plugin and are responsible for the acquisition of log data, there are two main types of sources:

- **Artifact Source**: A source that requires obtaining artifacts, loading them into memory and extracting logs entries.
- **Custom Source**: A source that requires a custom implementation to obtain log entries, such as an API.

Sources should return a stream of log entries in the native format of acquisition, these could be predefined structs, strings (log lines), etc.

### Table

Tables reside in the `tables` package of the plugin and are responsible for the collection of log entries in the format of the schema.

This is done by utilising a configured source, mapper(s) and row struct along with performing the Enrichment.


