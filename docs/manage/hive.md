---
title: Hive
---

# Hive

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

