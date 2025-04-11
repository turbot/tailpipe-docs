---
title: tailpipe partition
---

# tailpipe partition

List, show, and delete Tailpipe partitions.

## Usage
```bash
tailpipe partition list [args]
tailpipe partition show table_name.partition_name [args]
tailpipe partition delete table_name.partition_name [args]
```

## Sub-Commands

| Command | Description
|-|-
| [list](#tailpipe-partition-list) | List all partitions.
| [show](#tailpipe-partition-show)  | Show details of a partition.
| [delete](#tailpipe-partition-delete) | Delete a partition.


## tailpipe partition list
List all partitions.

### Arguments

| Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output format: `json`, `plain`, `pretty` (default) 

### Examples

List all partitions in table format.

```bash
tailpipe partition list
```

List all partitions in `JSON` format. 

```bash
tailpipe partition list --output json
```

## tailpipe partition show
Show details for a partition.

Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output format: `json`, `plain`, `pretty` (default) 


### Examples

Show details in text format.

```bash
tailpipe partition show aws_cloudtrail-log.prod
```

Show details in JSON format.

```bash
tailpipe partition show aws_cloudtrail-log.prod --output json
```

## tailpipe partition delete
Delete a partition.

### Arguments

| Flag | Description
|-|-
|  `--force`     |  Force delete without confirmation
|  `--from`      |  Specify the start time
|  `--help`      |  Help for delete


<!--
|  `--to`        |  Delete days older than a relative or absolute date.
-->

### Examples

Delete an entire partition.

```bash
tailpipe partition delete aws_cloudtrail_log.dev
```

Delete days newer than a relative date.

```bash
tailpipe partition delete aws_cloudtrail_log.dev --from T-7d
```

Delete days newer than an absolute date with no confirmation.

```bash
tailpipe partition delete aws_cloudtrail_log.dev --from 2024-01-01  --force
```
<!--
Delete days older than a relative date.

```bash
tailpipe partition delete aws_cloudtrail_log.dev --to T-7d
```

Delete days older than an absolute date.

```bash
tailpipe partition delete aws_cloudtrail_log.dev --to 2024-01-01
```
-->