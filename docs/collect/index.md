---
title: Collect Logs
---

# Collect Logs

Before you can query your logs with Tailpipe, you have to collect them.  Collection is the process of ingesting data from a source, such as an S3 bucket, file path, or API and transforming it into rows in a DuckDB table.


Tailpipe makes this process simple:
- Install [plugins](/docs/collect/plugins) for the log types you will collect.
- Define one or more [partitions](/docs/collect/define-partitions) to import data from one or more [sources](/docs/collect/define-partitions#sources) into parquet files that appear as rows in a table.
- Run `tailpipe collect` to [collect your logs](/docs/collect/collect).

## Concepts

When you [collect](/docs/collect/collect) logs from a source, e.g. an S3 bucket containing Cloudtrail logs, you invoke a [plugin](/docs/collect/plugins) that uses a [connection](/docs/manage/connection) to ingest logs from a [source](/docs/collect/define-partitions#sources) into a database table. 

The unit of collection is the [partition](/docs/collect/define-partitions), and a table may comprise one one or more partitions. The collection process writes to a [hive-partitioned](/docs/manage/partition#hive-partitioning) hierarchy of folders and files, over which Tailpipe builds the database table that you query. 

<!--
The [workspace](/docs/manage/workspace), by default `~/.tailpipe/data/default`, defines the location of the hive.
-->