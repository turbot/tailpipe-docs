---
title: Collect Logs
---

# Collect Logs

Before you can query your logs with Tailpipe, you have to collect them.  Collection is the process of ingesting data from a source, such as an S3 bucket, file path, or API and transforming it into rows in a DuckDB table.


Tailpipe makes this process simple:
- Install [plugins]() for the log types you will collect.
- Create [connections]() to define the credentials and configuration options to connect to your external services.
- Define one or more [partitions]() to import data from one or more [sources]() into parquet files that appear as rows in a [table]().
- Run `tailpipe collect` to [collect your logs]().

## Concepts

When you [collect](/docs/manage/collection) logs from a source, e.g. an S3 bucket containing Cloudtrail logs, you invoke a [plugin](/docs/manage/plugin) that uses a [connection](/docs/manage/connection) to read a [source](/docs/manage/source) and build a database [table](/docs/manage/table). 

The unit of collection is the [partition](/docs/manage/partition), and a table may comprise one one or more partitions. The collection process writes to a hierarchy of folders and files called the [hive](/docs/manage/partition#hive-partitioning), over which Tailpipe builds the database table that you query. 

The [workspace](/docs/manage/workspace), by default `~/.tailpipe/data/default`, defines the location of the hive.




<!--

---
title: Manage Tailpipe
---

# Manage Tailpipe

Tailpipe is simple to install, it's distributed as a single binary file that you can [download and run](/downloads). Use Tailpipe to collect logs, then query them. 

## Concepts

When you [collect](/docs/manage/collection) logs from a source, e.g. an S3 bucket containing Cloudtrail logs, you invoke a [plugin](/docs/manage/plugin) that uses a [connection](/docs/manage/connection) to read a [source](/docs/manage/source) and build a database [table](/docs/manage/table). 

The unit of collection is the [partition](/docs/manage/partition), and a table may comprise one one or more partitions. The collection process writes to a hierarchy of folders and files called the [hive](/docs/manage/partition#hive-partitioning), over which Tailpipe builds the database table that you query. 

The [workspace](/docs/manage/workspace), by default `~/.tailpipe/data/default`, defines the location of the hive.




-->