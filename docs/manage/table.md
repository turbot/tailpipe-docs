---
title: Table
---

# Table

Tables are implemented as DuckDB views over the Parquet files.  Tailpipe creates tables (that is, creates views in the `tailpipe.db` database) based on the data and metadata that it discovers in the [workspace](#workspaces), along with the filter rules.

When Tailpipe starts, it finds all the tables in the workspace according to the [hive directory layout](/docs/manage/hive).  For each schema, it adds a view for the table.  The view definitions will include qualifiers that implement the filter rules that are defined in the [schema definition](#schemas).

## Common fields

Tailpipe plugins populate a set of common fields. Some are mandatory, for example `tp_partition` and `tp_date`. Others, like `tp_source_ip` and `tp_ips`, are optional. Plugins map table-specific fields to these common fields when it is appropriate to do so. The AWS Cloudtrail plugin, for example, maps the value of the native field `SourceIPAddress` to the common field `tp_source_ip`. It also adds that address to the `tp_ips` array.

These mappings enable queries that correlate values across different logs. If you have collected both Cloudtrail and ALB logs, for example, you could query for source addresses that occur in both the `aws_cloudtrail_log` and `aws_alb_access_log` tables.

| Field Name | Type |
|------------|------|
| tp_akas | varchar[] |
| tp_date | date |
| tp_destination_ip | varchar |
| tp_domains | varchar[] |
| tp_emails | varchar[] |
| tp_id | varchar |
| tp_index | varchar |
| tp_ingest_timestamp | timestamp |
| tp_ips | varchar[] |
| tp_partition | varchar |
| tp_source_ip | varchar |
| tp_source_location | varchar |
| tp_source_name | varchar |
| tp_source_type | varchar |
| tp_table | varchar |
| tp_tags | varchar[] |
| tp_timestamp | timestamp |
| tp_usernames | varchar[] |
