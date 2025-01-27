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
  }
  
}
``` 

The data for each table partition will be stored in its own subdirectory in the [hive](/docs/reference/glossary#hive).

At query time, Tailpipe discovers partitions in the workspace and automatically creates tables based on the partitions it finds.  For instance, if you define three `aws_cloudtrail_log` partitions, the `aws_cloudtrail_log` table will include the data from all three.

## Take advantage of hive partitioning

You can speed up a query by using a `where` or `join` clause to restrict the number of files Tailpipe will read to satisfy the query. This restriction operates at three levels.

*Partition*. When a table defines more than one partition, you can filter to include only files belonging to other partitions.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod'
```

*Index*. When a partition defines more than one index, you can filter to include  all files belonging to other indexes.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod' and index = 123456789
```

*Date*. Each file contains log data for one day. You can filter to include only files for that day.

```sql
select count(*) from aws_cloudtrail_log where partition = 'prod' and index = 123456789 and tp_date = '2024-12-01'
```


