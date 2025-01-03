---
title: Tables
---

# Tables

>[!NOTES]
> Not much more here than in the glossary. But maybe we expand on schema definitions here. Unsure how much of that concept surfaces for lw7 vs later.

Tables are implemented as DuckDB views over the parquet files.  Tailpipe creates tables (that is, creates views in the `tailpipe.db` database) based on the data and metadata that it discovers in the [workspace](#workspaces), along with the filter rules.

When Tailpipe starts, it finds all the tables in the workspace according to the [hive directory layout](/docs/reference/concepts/hive).  For each schema, it adds a view for the table.  The view definitions will include qualifiers that implement the filter rules that are defined in the [schema definition](#schemas).