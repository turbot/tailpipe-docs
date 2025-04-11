---
title: tailpipe compact
---

# tailpipe compact

Compact multiple Parquet files per day to one per day.

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
    Sets the location to search for <a href = "/docs/reference/config-files/">configuration files</a>. The default is `$TAILPIPE_INSTALL_DIR/config`, eg `~/.tailpipe/config`.
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

<!--
Compact a table's newest 7 days:

```hcl
tailpipe compact aws_cloudtrail_log  --from T-7d
```

Compact a table between two dates:

```hcl
tailpipe compact aws_cloudtrail_log  --from 1/1/2024 --to 2/1/2024
```
-->

Compact a partition:

```
tailpipe compact aws_cloudtrail_log.prod
```

<!--
Compact specific days for partition:

```hcl
tailpipe compact aws_cloudtrail_log.prod  --from 1/1/2024 --from 2/1/2024
tailpipe compact aws_cloudtrail_log.prod  --from T-7d
```
-->

