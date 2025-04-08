---
title: Partitions
---

# Partitions

A partition represents data gathered from a [source](/docs/manage/source). Partitions are defined [in HCL](/docs/reference/config-files/partition) and are required for [collection](/docs/collect/collect).  

The partition has two labels:

1. The [table](/docs/manage/table) name. The table name is meaningful and must match a table name for an installed [plugin](/docs/collect/plugins) or a [custom table](manage/table#custom-tables). The table name implies the shape of resulting Parquet file, and also makes assumptions about the source data format.  For example, the `aws_cloudtrail_log` table is defined in the AWS plugin. The shape of that table — the structure of the data in the destination Parquet file, which corresponds to table columns — is defined in the AWS plugin.

2. A partition name.  The partition name must be unique for all partitions in a given table (though different tables may use the same partition names).  

```hcl
partition "aws_cloudtrail_log" "test" {

  source "aws_s3" {
    connection = connection.aws.logs
    bucket     = "my-logs-bucket"
  }
  
}
``` 

The data for each table partition will be stored in its own subdirectory in the [hive](#hive-partitioning).

At query time, Tailpipe discovers partitions in the [workspace](/docs/manage/workspace) and automatically creates tables based on the partitions it finds.  For instance, if you define three `aws_cloudtrail_log` partitions, the `aws_cloudtrail_log` table will include the data from all three.



## Hive partitioning

Tailpipe uses [hive partitioning](https://duckdb.org/docs/data/partitioning/hive_partitioning.html) to leverage automatic [filter pushdown](https://duckdb.org/docs/data/partitioning/hive_partitioning.html#filter-pushdown) and Tailpipe is opinionated on the layout:

  - The data is written to Parquet files in the workspace directory, with a prescribed directory and filename structure.  Other than **index** the layout is dictated by the Tailpipe core.

  - The *plugin* may choose the **index** value, but it is not *user*-definable

The standard partitioning/hive structure enables efficient queries that only need to read subsets of the hive filtered by index or date. 



### Index: Custom Partition Key 

Each plugin chooses what the **index** is for a given table.   Because the data is laid out into partitions,  performance is optimized when the partition appears in a `where` or `join` clause.  The index provides a way to segment the data to optimize lookup performance in a way that is *optimal for the specific plugin*.  For example, AWS tables index on account id, Azure tables on subscription, and GCP on project id. 

```bash
tp_table=aws_cloudtrail_log
└── tp_partition=prod
    └── tp_index=605491513981
        ├── tp_date=2024-12-31
        │   └── data_20250106140713_740378_0.parquet
        ├── tp_date=2025-01-01
        │   └── data_20250106140713_740378_0.parquet
        ├── tp_date=2025-01-02
        │   └── snap_20250106140823_952067.parquet
        ├── tp_date=2025-01-03
        │   └── snap_20250106140824_011599.parquet
        ├── tp_date=2025-01-04
        │   └── data_20250106140752_829722_0.parquet
        ├── tp_date=2025-01-05
        │   └── snap_20250106140824_073116.parquet
        └── tp_date=2025-01-06
            └── snap_20250106140824_131637.parquet
```


