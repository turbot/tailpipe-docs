---
title: tailpipe collect
---

# tailpipe collect

Run a **collection**.

## Usage
```bash
 tailpipe collect [table|table.partition] [flags]
 ```

### Arguments

| Flag | Description
|-|-
|  `--compact`       | Compact the Parquet files after collection (default true)
|  `--from string`   | Collect days newer than a relative or absolute date.
|  `--help`          |  Help for collect



## Examples

Collect everything.

```bash
tailpipe collect
```

Collect all partitions in the `aws_cloudtrail_log` table.

```bash
tailpipe collect aws_cloudtrail_log
```

Collect one partition in the `aws_cloudtrail_log` table.

```bash
tailpipe collect aws_cloudtrail_log.dev
```

Collect all partitions in the `aws_cloudtrail_log` table for the last 45 days.

```bash
tailpipe collect aws_cloudtrail_log --from T-45d
```

<!--
Collect all partitions in the `aws_cloudtrail_log` between January and June.

```bash
tailpipe collect aws_cloudtrail_log --from 1/1/2024 --to 6/30/2024
```
-->

Collect all partitions in the `aws_cloudtrail_log` table and output JSON.

```bash
tailpipe collect aws_cloudtrail_log --output json
```
