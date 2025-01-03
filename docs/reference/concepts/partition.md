---
title: Partitions
---

>[!NOTE]
> The word `plugin` appears nowhere here. Suggest separating, in a plugins guide, what the plugin DOES to create a partition, and here on what the partition IS.

# Partitions

During the **collection process**, a partition will accept rows from one or more **source**, optionally filter and/or standardize and write to a "refined" row-wise file (JSON, CSV, etc), then will read those files and convert the data to parquet files stored in a specific filesystem structure.  Each *partition* contains a set of data for a single table, though the *partition name* may be the same across different tables. ***expand:  why might you do this?***

The partition is responsible for:
  - modifying and standardizing the data from the *source*
  - generalized filtering based on row contents
  - writing parquet files

The partition does NOT:
  - transfer files or interact with external systems directly (the source does)
  - Create or manage views and tables in the schemas

Partitions are defined in HCL and are required for collection.  Note that the partition definitions (HCL) are *not* required for querying the tables. The actual table creation is done based on the [schema](#schemas) definition, the [directory structure](#hive-storage-format), and the metadata in the parquet files in the workspace directory.  In other words, Tailpipe creates tables based on the partitions and files that it discovers in the workspace directory, not the `partition` HCL definitions.  ***this means you wont see "empty" tables if you install a plugin but don't add data?  you cant "discover"/"inspect" a tables unless it is populated?***

The partition has 2 labels:
1. The table name.  The table name is meaningful and must match a table name for an installed plugin. The table name implies the shape of resulting parquet file, and also makes assumptions about the source data format.  For example, the `aws_cloudtrail_log` table is defined in the AWS plugin.  The shape of that table (the structure of the data in the destination parquet file, which ends up being the table columns) is defined in the AWS plugin.  An S3 `source` in that partition will be assumed to follow the standard AWS key prefix structure, and contain standard cloudtrail log files in json format.

2. A partition name.  The name of the partition.  The partition name must be unique for all partitions in a given table (though different tables may use the same partition names).  

```h
partition "aws_cloudtrail_log" "test" {

  source "aws_s3" {
    connection = connection.aws.logs
    bucket = "my-logs-bucket"
    prefix = "optional/path"
  }
  
}
``` 

The data for each table partition will be stored in its own subdirectory [hive structure](#hive-storage-format).  

Tailpipe will discover the partitions in the workspace and automatically create [Tables](#tables) based on the partitions it finds.   The default [schema](#schemas) will present all the tables, with each table including the data from all partitions of that type.  For instance, if you have 3 `aws_cloudtrail_log` partitions, the `aws_cloudtrail_log` table will include the data from all 3 by default.
