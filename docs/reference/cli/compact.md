---
title: tailpipe compact
---

Compact multiple Parquet files per day to one per day.

# tailpipe compact

## Usage
```bash
 tailpipe compact [table] [table.partition] [args]
 ```

### Arguments

| Flag | Description
|-|-
|  `--help`          |  Help for compact
|  `--output`        |  One of: json, text (default text)

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

Compact everything:

```hcl
tailpipe compact
```

Compact a table:

```hcl
tailpipe compact aws_cloudtrail_log
```

Compact a table's newest 7 days:

```hcl
tailpipe compact aws_cloudtrail_log  --from T-7d
```

Compact a table between two dates:

```hcl
tailpipe compact aws_cloudtrail_log  --from 1/1/2024 --to 2/1/2024
```

Compact a partition:

```
tailpipe compact aws_cloudtrail_log.prod
```

Compact specific days for partition:

```hcl
tailpipe compact aws_cloudtrail_log.prod  --from 1/1/2024 --from 2/1/2024
tailpipe compact aws_cloudtrail_log.prod  --from T-7d
```


