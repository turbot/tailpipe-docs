---
title: Partitions
---


# Partitions

Partitions are defined in HCL and are required for collection.  

The partition has two labels:

1. The table name. The table name is meaningful and must match a table name for an installed plugin. The table name implies the shape of resulting Parquet file, and also makes assumptions about the source data format.  For example, the `aws_cloudtrail_log` table is defined in the AWS plugin.  The shape of that table (the structure of the data in the destination Parquet file, which corresponds to table columns) is defined in the AWS plugin.

2. A partition name.  The partition name must be unique for all partitions in a given table (though different tables may use the same partition names).  

```hcl
partition "aws_cloudtrail_log" "test" {

  source "aws_s3" {
    connection    = connection.aws.logs
    bucket        = "my-logs-bucket"
    file_layout   = "%{DATA}.json.gz"
  }
  
}
``` 

The data for each table partition will be stored in its own subdirectory in the hive.

At query time, Tailpipe discovers partitions in the workspace and automatically creates tables based on the partitions it finds.  For instance, if you define three `aws_cloudtrail_log` partitions, the `aws_cloudtrail_log` table will include the data from all three.


