---
title: Hive
---

# Hive

Tailpipe uses [hive partitioning](https://duckdb.org/docs/data/partitioning/hive_partitioning.html) to leverage automatic [filter pushdown](https://duckdb.org/docs/data/partitioning/hive_partitioning.html#filter-pushdown) and tailpipe is opinionated on the layout:
  - The data is written in parquet files the workspace directory, with a prescribed directory and filename structure.  Other than **index** the layout is dictated by tailpipe core.
  - The *plugin* may choose the **index** value, but it is not *user*-definable
  - The metadata is also written in parquet files the workspace directory, with a prescribed directory and filename structure. 

The standard partitioning/hive structure is important to make querying efficient.  Tailpipe [schemas](#schemas) also depend on this structure, as the filterable fields are aligned to the hive partitions.

### Index: Custom Partition Key 

Each plugin chooses what the **index** is for a given table.   Because the data is laid out into partitions,  performance is optimized when the partition appears in a `where` or `join` clause, or is used on a **schema**  definition.  The index provides a way to segment the data to optimize lookup performance in a way that is *optimal for the specific plugin*.  For example, AWS tables might index on account id, Azure tables might index by subscription, GCP may index by project id, nginx logs may partition by hostname, github may partition by repo url, etc.

```bash
/Users/jsmyth/.tailpipe/db/default
├── data
│  └── tp_table=aws_cloudtrail_log
│      └── tp_partition=default
│          └── tp_index=811596193553
│             ├── tp_date=2017-02-12
│             │   └── data_0.parquet
│             ├── tp_date=2017-02-18
│             │   └── data_0.parquet
│             ├── tp_date=2017-02-19
│             │   └── data_0.parquet
│             ├── tp_date=2017-02-20
│             │   └── data_0.parquet
│             ├── tp_date=2017-02-22
│             │   └── data_0.parquet
│             └── tp_date=2017-02-23
│                 └── data_0.parquet
│   
│
└── metadata
   └── tp_table=aws_cloudtrail_log
       └── tp_partition=default
               │── catalog_399af3e3-df50-4dd9-9a34-e82f77196bc6.parquet
               └── catalog_1a00f99f-af25-4f2e-a70d-87b5cab300c7.parquet
```

