---
title: Collection
---

# Collection

The [tailipe collect](/docs/reference/cli/collect) command runs a [plugin](/docs/reference/concepts/plugin) that reads from a [source](/docs/reference/concepts/source) and writes to the [hive](/docs/reference/concepts/hive). Every time you run `tailpipe collect`, Tailpipe refreshes its views over all collected parquet files. Those views are the tables you query with `tailpipe query` (or with DuckDB, or another client).

The collection process always writes to a local **workspace**, and does so on a per-partition basis.  While you may specify multiple partitions on the command line, `partition` is the unit of collection.  A partition day is the atomic unit of work; the partition collection succeeds or fails for all sources for a given day, and if it fails, rolls everything back for that day.

When a partition is collected, each source resumes from the last time it was collected.  Source data is ingested, standardized, then written to parquet files in the **standard hive structure**.  

>[NOTE]
> manifest explanation omitted, related to push/pull, out of scope for lw7?

 {/*

In addition, [metadata manifest](#catalog-metadata-files) data is written for the collection run.  The metadata files allow Tailpipe to efficiently determine what files to push/pull, as well as data to estimate the size of tables, partitions, and potential downloads or uploads.  The manifest files are parquet files that include the name and path of each file written, as well as summary data for each file such as the size and number of rows.  A given partition may have multiple manifest files which comprise an append-only log from which a consistent partition state can be determined.  Manifests are pulled when data is pulled.  The manifests that have been pulled from the remote will not be written to though;  metadata for collection is written to a new manifest file, and subsequent collection runs on will append to this file until it is pushed, at which time a new empty local manifest will be created.  The manifest files are immutable once written to the remote.  As a result, multiple Tailpipe instances may write data to the remote simultaneously.

It is possible to separate the collection process from querying, and to "federate" the collection process by having multiple workers collecting at the same time and pushing to the same remote.  As a best practice, however, a single partition for a single table should only be collected by a single instance, or they will likely end up duplicating data.
*/}

### Initial Collection

Often, the source data to be ingested is large, and the first ingestion would take quite a long time. To improve the first-run experience for collection Tailpipe collects by days in reverse chronological order. In other words, it starts with the current day and moves backward. The default is NOT be to collect all data on the initial collection, there is a default 30-day lookback window. You can override that on the command line, e.g.:

```
tailpipe collect aws_cloudtrail_log.test --from T-180d
tailpipe collect aws_cloudtrail_log.test --from 2024-01-01 --to 2024-03-31
    ```
- Subsequent collection runs should occur chronologically resuming from the last collection by default, so there are no time gaps while the data is being collected.
  - A user may specify a specify time range using `--from` and `--to` but generally this is discouraged as it potentially leaves gaps in the data
- The data should be available for querying even while partition collection is still occurring.

