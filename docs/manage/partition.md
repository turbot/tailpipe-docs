---
title: Partition
---

# Partition

  A partition represents data gathered from a [source](/docs/manage/source). Partitions are defined [in HCL](/docs/reference/config-files/partition) and are required for [collection](/docs/manage/collection).  

The partition has two labels:

1. The [table](/docs/manage/table) name. The table name is meaningful and must match a table name for an installed [plugin](/docs/manage/plugin). The table name implies the shape of resulting Parquet file, and also makes assumptions about the source data format.  For example, the `aws_cloudtrail_log` table is defined in the AWS plugin. The shape of that table — the structure of the data in the destination Parquet file, which corresponds to table columns — is defined in the AWS plugin.

2. A partition name.  The partition name must be unique for all partitions in a given table (though different tables may use the same partition names).  

```hcl
partition "aws_cloudtrail_log" "test" {

  source "aws_s3" {
    connection = connection.aws.logs
    bucket     = "my-logs-bucket"
  }
  
}
``` 

The data for each table partition will be stored in its own subdirectory in the [hive](/docs/manage/hive).

At query time, Tailpipe discovers partitions in the [workspace](/docs/manage/workspace) and automatically creates tables based on the partitions it finds.  For instance, if you define three `aws_cloudtrail_log` partitions, the `aws_cloudtrail_log` table will include the data from all three.

### Partition index

The partition index is defined by the plugin author who may choose to define meaningful segments within a partition. The AWS plugin, for example, uses `account_id` for the index.  

Queries can then slice the table by each profile's `account_id`.


