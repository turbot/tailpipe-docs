---
title: Data Storage
---

#  Data Storage


Tailpipe uses a hive-partitioned storage structure that organizes data for efficient querying. Let's look at how data is stored:

```bash
tp_table=nginx_access_log
└── tp_partition=dev
    ├── tp_index=dev1
    │   ├── tp_date=2024-11-12
    │   │   ├── file_2b8c5008-09d6-4ada-9065-32263a4f5539.parquet
    │   │   ├── file_4ccaaed5-0c2b-46c6-9c07-75cd4d086051.parquet
    │   │   ├── file_6252d495-dae3-43f1-a77f-14d1408af2f8.parquet
    │   │   ├── file_c50479b6-0684-43a5-917a-95b1e003358e.parquet
```

The structure has several key components:
- **Partition**: Groups data by source (e.g., `nginx_access_log`)
- **Index**: Sub-divides data by a meaningful key (e.g., server name for NGINX logs)
- **Date**: Further partitions data by date
- Each partition contains parquet files with the actual log data

This hierarchical structure enables efficient querying through partition pruning. When you query with conditions on `tp_partition`, `tp_index`, or `tp_date`, Tailpipe (and DuckDB) can skip reading irrelevant parquet files entirely.

