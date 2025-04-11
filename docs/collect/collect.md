---
title: Run Collection
---

# Run Collection

The [`tailpipe collect`command ](/docs/reference/cli/collect) runs a [plugin](/docs/collect/plugins) that reads from a [source](/docs/collect/configure#sources) and writes to the [hive](/docs/collect/configure#hive-partitioning). Every time you run `tailpipe collect`, Tailpipe refreshes its views over all collected Parquet files. Those views are the tables you query with `tailpipe query`.

Examples:

Collect everything.

```bash
tailpipe collect
```

Collect all partitions in the `aws_cloudtrail_log` table.

```bash
tailpipe collect aws_cloudtrail_log
```

Collect a specific partition.

```bash
tailpipe collect aws_cloudtrail_log.dev
```

See [collect](/docs/reference/cli/collect) for more examples.


The collection process always writes to a local [workspace](/docs/reference/config-files/workspace) and does so on a per-partition basis.  While you may specify multiple partitions on the command line, `partition` is the unit of collection.

<!--
A partition day is the atomic unit of work; the partition collection succeeds or fails for all sources for a given day, and if it fails, it rolls everything back for that day.
-->

When a [partition](/docs/collect/configure#partitions) is collected, each source resumes from the last time it was collected. Source data is ingested, standardized, and then written to Parquet files in the standard [hive](/docs/collect/configure#hive-partitioning).

Queries can slice the data by partition using the `tp_partition` field.

### Initial collection

Often, the source data to be ingested is large, and the first ingestion would take quite a long time. To improve the first-run experience for collection, Tailpipe attempts to collect in reverse chronological order. In other words, it starts with the current day and moves backward.  By default, Tailpipe will only collect the last 7 days during the initial collection.  You can override that on the command line, e.g.:

```bash
tailpipe collect aws_cloudtrail_log.test --from T-180d
```

```bash
tailpipe collect aws_cloudtrail_log.test --from 2024-01-01
```

Subsequent collection runs occur chronologically, resuming from the last collection by default, so there are no time gaps while the data is being collected.

<!--
- The data is available for querying even while partition collection is still occurring.
-->

