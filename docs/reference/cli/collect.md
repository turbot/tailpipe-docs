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

### Global Flags

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

Collect all partitions in the `aws_cloudtrail_log` between January and June.

```bash
tailpipe collect aws_cloudtrail_log --from 1/1/2024 --to 6/30/2024
```

Collect all partitions in the `aws_cloudtrail_log` table and output JSON.

```bash
tailpipe collect aws_cloudtrail_log --output json
```
