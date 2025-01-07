---
title: Manage Collection
---

# Manage Collection

The [tailpipe collect](/docs/reference/cli/collect) command runs a [plugin](/docs/manage/plugin) that reads from a [source](/docs/manage/source) and writes to the [hive](/docs/manage/hive). Every time you run `tailpipe collect`, Tailpipe refreshes its views over all collected parquet files. Those views are the tables you query with `tailpipe query` (or directly with DuckDB).

The collection process always writes to a local **workspace**, and does so on a per-partition basis.  While you may specify multiple partitions on the command line, `partition` is the unit of collection.  A partition day is the atomic unit of work; the partition collection succeeds or fails for all sources for a given day, and if it fails, rolls everything back for that day.

When a partition is collected, each source resumes from the last time it was collected.  Source data is ingested, standardized, then written to parquet files in the **standard hive structure**.  

### Initial collection

Often, the source data to be ingested is large, and the first ingestion would take quite a long time. To improve the first-run experience for collection Tailpipe collects by days in reverse chronological order. In other words, it starts with the current day and moves backward. The default is NOT be to collect all data on the initial collection, there is a default 7-day lookback window. You can override that on the command line, e.g.:

```
tailpipe collect aws_cloudtrail_log.test --from T-180d
tailpipe collect aws_cloudtrail_log.test --from 2024-01-01 --to 2024-03-31
```

- Subsequent collection runs occur chronologically resuming from the last collection by default, so there are no time gaps while the data is being collected.

- A user may specify a specify time range using `--from` and `--to` but generally this is discouraged as it potentially leaves gaps in the data.

- The data is available for querying even while partition collection is still occurring.

### Tables, partitions, and indexes

The configuration file for collection minimally specifies one partition, and a partition minimally specifes one index: the common `tp_index` field. 

The author of a configuration file can route one or more sources into a partition, and can define multiple partions. 

The partition index is defined by the plugin author who may choose to define meaningful segments within a partition. The AWS plugin, for example, uses `account_id` for the index.  This is optional for the plugin author, though, and for some plugins the `tp_index` will always be the same value.

The values of a partition index are determined by the author of a configuration file who may, for example, specify an AWS table that sources from several AWS profiles. Queries can then slice the table by each profile's `account_id`.



