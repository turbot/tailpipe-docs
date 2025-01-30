---
title: tailpipe connect
---

# tailpipe connect

Return a connection string for a database, with a schema determined by the provided parameters.

## Usage
```bash
 tailpipe connect [flags]
 ```

### Arguments

| Flag | Description
|-|-
| `--from string`    |  Specify the start time
|  `--help`          |  Help for connect
| `--index strings`      |  Specify the index(es) to use
|  `--output`        |  One of: json, text (default text)
| `--partition strings`  |  Specify the partition(s) to use
| `--to string`      |  Specify the end time

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

Connect to the Tailpipe database, filtered to records after the start time:

```bash
tailpipe connect --from 2025-01-01
```

```bash
/home/jon/.tailpipe/data/default/tailpipe_20250115140447.db
```

> [!NOTE]
> You could use this connection string with DuckDB:
> 
> duckdb /home/jon/.tailpipe/data/default/tailpipe_20241212134120.db

Connect with no filter, show output as json:

```bash
tailpipe connect --output json
```

```bash
{"database_filepath":"/Users/jonudell/.tailpipe/data/default/tailpipe_20250129204416.db"}
```

