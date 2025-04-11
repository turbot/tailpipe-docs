---
title: tailpipe table
---

# tailpipe table

List and show Tailpipe tables.

## Usage
```bash
tailpipe table_name list [args]
tailpipe table_name show [args]
```

## Sub-Commands

| Command | Description
|-|-
| [list](#tailpipe-table-list) | List all tables.
| [show](#tailpipe-table-show)  | Show details of a table.


## tailpipe table list
List all tables.

### Examples

List tables.

```bash
tailpipe table list
```

List all tables in `JSON` format. 

```bash
tailpipe table list --output json
```

## tailpipe table show
Show details for a table.

### Examples

Show details in table format.

```bash
tailpipe table show pipes_audit_log.prod
```

Show details in JSON format.

```bash
tailpipe table show pipes_audit_log.prod --output json
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
    Sets the location to search for <a href = "/docs/reference/config-files/">configuration files</a>. The default is `$TAILPIPE_INSTALL_DIR/config`, eg `~/.tailpipe/config`.
    </td> 
  </tr>


  <tr> 
    <td nowrap="true"> `--workspace	`  </td> 
    <td>  Sets the Tailpipe workspace profile. If not specified, the default workspace will be used if it exists. See <a href="/docs/reference/config-files/workspace">workspace</a> for details. </td> 
  </tr>

</table>

