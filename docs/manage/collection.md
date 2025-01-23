---
title: Collection
---

# Collection

The [tailpipe collect](/docs/reference/cli/collect) command runs a [plugin](/docs/manage/plugin) that reads from a [source](/docs/manage/source) and writes to the [hive](/docs/manage/hive). Every time you run `tailpipe collect`, Tailpipe refreshes its views over all collected Parquet files. Those views are the tables you query with `tailpipe query` (or directly with DuckDB).

The collection process always writes to a local **workspace**, and does so on a per-partition basis.  While you may specify multiple partitions on the command line, `partition` is the unit of collection.  A partition day is the atomic unit of work; the partition collection succeeds or fails for all sources for a given day, and if it fails, rolls everything back for that day.

When a partition is collected, each source resumes from the last time it was collected.  Source data is ingested, standardized, then written to Parquet files in the **standard hive structure**.  

### Initial collection

Often, the source data to be ingested is large, and the first ingestion would take quite a long time. To improve the first-run experience for collection Tailpipe collects by days in reverse chronological order. In other words, it starts with the current day and moves backward. The default is NOT be to collect all data on the initial collection, there is a default 7-day lookback window. You can override that on the command line, e.g.:

```
tailpipe collect aws_cloudtrail_log.test --from T-180d
tailpipe collect aws_cloudtrail_log.test --from 2024-01-01
```

- Subsequent collection runs occur chronologically resuming from the last collection by default, so there are no time gaps while the data is being collected.

- The data is available for querying even while partition collection is still occurring.

### Tables, partitions, and indexes

The configuration file for collection minimally specifies one partition, and a partition minimally specifes one index: the common `tp_index` field. 

The author of a configuration file can route one or more sources into a partition, and can define multiple partions. 

The partition index is defined by the plugin author who may choose to define meaningful segments within a partition. The AWS plugin, for example, uses `account_id` for the index.  This is optional for the plugin author, though, and for some plugins the `tp_index` will always be the same value.

The values of a partition index are determined by the author of a configuration file who may, for example, specify an AWS table that sources from several AWS profiles. Queries can then slice the table by each profile's `account_id`.

### Common fields

Tailpipe plugins populate a set of common fields. Some are mandatory, for example `tp_partition` and `tp_date`. Others, like `tp_source_ip` and `tp_ips`, are optional. Plugins map table-specific fields to these common fields when it is appropriate to do so. The AWS Cloudtrail plugin, for example, maps the value of the native field `SourceIPAddress` to the common field `tp_source_ip`. It also adds that address to the `tp_ips` array.

These mappings enable queries that correlate values across different logs. If you have collected both Cloudtrail and ALB logs, for example, you could query for source addresses that occur in both the `aws_cloudtrail_log` and `aws_alb_access_log` tables.

```go
type CommonFields struct {
	// Mandatory fields
	TpID              string    `json:"tp_id"`
	TpSourceType      string    `json:"tp_source_type"`
	TpIngestTimestamp time.Time `json:"tp_ingest_timestamp"`
	TpTimestamp       time.Time `json:"tp_timestamp"`

	// Hive fields
	TpTable     string    `json:"tp_table"`
	TpPartition string    `json:"tp_partition"`
	TpIndex     string    `json:"tp_index"`
	TpDate      time.Time `json:"tp_date" parquet:"type=DATE"`

	// Optional fields
	TpSourceIP       *string `json:"tp_source_ip"`
	TpDestinationIP  *string `json:"tp_destination_ip"`
	TpSourceName     *string `json:"tp_source_name"`
	TpSourceLocation *string `json:"tp_source_location"`

	// Searchable
	TpAkas      []string `json:"tp_akas,omitempty"`
	TpIps       []string `json:"tp_ips,omitempty"`
	TpTags      []string `json:"tp_tags,omitempty"`
	TpDomains   []string `json:"tp_domains,omitempty"`
	TpEmails    []string `json:"tp_emails,omitempty"`
	TpUsernames []string `json:"tp_usernames,omitempty"`
}
```