---
title: Glossary
---


# Glossary

## Collection

A collection is the set of per-day parquet files created by a plugin when you run the `tailpipe collect` command. 

## Connection

A connection is a HCL configuration item that represents a reusable connection to a service, i.e. AWS access keys, region, etc.

## DuckDB

Tailpipe uses DuckDB, an embeddable column-oriented database.

## Hive

A tree of parquet files in `~/.tailpipe/data/default` rooted at, e.g., `tp_table=aws_cloudtrail_log`. The `tailpipe.db` in `~/.tailpipe/data/default` (and derivatives created by `tailpipe connect`, e.g. `tailpipe_20241212152506.db`), are thin wrappers that materialize views over the parquet data.

## Source

`source` is an argument that appears in .tpc files. Example values: `source "aws_s3_bucket"`, `source "file_system"`, `source "pipes_audit_log_api"`.

## Parquet

Parquet is a columnar storage file format that is optimized for reading and writing large datasets. It is the backing file format of the logs collected via Tailpipe, these are exposed by DuckDB.

## Partition

A partition is a HCL configuration item (e.g. `partition "aws_cloudtrail_log" "prod") that represents an instance of a table with provided table and source configurations. They are defined in  `.tpc` configuration files in the Tailpipe installation's config directory (e.g. `~/.tailpipe/config`).

## Table

Every Tailpipe query refers to one more tables built by the collection process (e.g. `select count(*) from aws_cloudtrail_log`).

