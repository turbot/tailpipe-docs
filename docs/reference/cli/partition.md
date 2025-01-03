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

### Examples

List partitions in table format.

```bash
tailpipe partition list
```

List all partitions in `JSON` format. 

```bash
tailpipe partition list --output json
```

## tailpipe partition show
Show details for a partition.

### Examples

Show details in table format.

```bash
tailpipe partition show github_audit_log.prod
```

Show details in JSON format.

```bash
tailpipe partition show github_audit_log.prod --output json
```


## tailpipe partition delete
Delete a partition.

### Arguments

| Flag | Description
|-|-
|  `--force`     |  No confirmation.
|  `--from`      |  Delete days newer than a relative or absolute date.
|  `--help`      |  Show help.
|  `--to`        |  Delete days older than than a relative or absolute date.


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
tailpipe partition delete aws_cloudtrail_log.dev --from 1/1/24 --force
```

Delete days older than a relative date.

```bash
tailpipe partition delete aws_cloudtrail_log.dev --to T-7d
```

Delete days older than an absolute date.

```bash
tailpipe partition delete aws_cloudtrail_log.dev --to 1/1/24
```


