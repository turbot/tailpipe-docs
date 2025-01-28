---
title: Manage Tailpipe
---

# Manage Tailpipe

Tailpipe is simple to install, it's distributed as a single binary file that you can [download and run](/downloads). Use Tailpipe to collect logs, then query them. 

## Concepts

When you [collect](/docs/manage/collection) logs from a source, e.g. an S3 bucket containing Cloudtrail logs, you invoke a [plugin](/docs/manage/plugin) that uses a [connection](/docs/manage/connection) to read a [source](/docs/manage/source) and build a database [table](/docs/manage/table). 

The unit of collection is the [partition](/docs/manage/partition), and a table may comprise one one or more partitions. The collection process writes to a hierarchy of folders and files called the [hive](/docs/manage/hive), over which Tailpipe builds the database table that you query. 

The [workspace](/docs/manage/workspace), by default `~/.tailpipe/data/default`, defines the location of the hive.




