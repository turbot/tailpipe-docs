---
title: tailpipe partition
---

# tailpipe partition

List, show, and delete Tailpipe partitions.

## Usage
```bash
tailpipe partition delete table_name.partition_name [args]
tailpipe partition list [args]
tailpipe partition show table_name.partition_name [args]
```

## Sub-Commands

| Command | Description
|-|-
| [delete](#tailpipe-partition-delete) | Delete a partition.
| [list](#tailpipe-partition-list) | List all partitions.
| [show](#tailpipe-partition-show)  | Show details of a partition.

## tailpipe partition delete
Delete a partition.

### Arguments

| Flag | Description
|-|-
|  `--force`     |  Force delete without confirmation
|  `--from`      |  Specify the start time
|  `--help`      |  Help for delete

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

## tailpipe partition list
List all partitions.

### Arguments

| Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output format: json, text (default)
|  `--to`        |  Delete days older than than a relative or absolute date.

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

Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output format: json, text (default)


### Examples

Show details in text format.

```bash
tailpipe partition show aws_cloudtrail-log.prod
```

Show details in JSON format.

```bash
tailpipe partition show aws_cloudtrail-log.prod --output json
```



## Global Flags

<table>
  <tr> 
    <th> Flag </th> 
    <th> Description </th> 
  </tr>

  <tr> 
    <td nowrap="true"> `--config-path`</td> 
    <td>  
    Sets the search path for <a href = "/docs/reference/config-files">configuration files</a>. This argument accepts a colon-separated list of directories.  All  configuration files (`*.tpc`) will be loaded from each path, with decreasing precedence.  The default is `.:$TAILPIPE_INSTALL_DIR/config` (`.:~/.tailpipe/config`).  This allows you to manage your <a href="/docs/reference/config-files/workspace"> workspaces </a> and <a href="/docs/reference/config-files/connection">connections</a> centrally in the `~/.tailpipe/config` directory, but override them in the working directory / mod location if desired.
    </td> 
  </tr>


  <tr> 
    <td nowrap="true"> `--workspace	`  </td> 
    <td>  Sets the Tailpipe workspace profile. If not specified, the default workspace will be used if it exists. See <a href="/docs/reference/config-files/workspace">workspace</a> for details. </td> 
  </tr>

</table>



