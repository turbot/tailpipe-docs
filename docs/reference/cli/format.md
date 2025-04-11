---
title: tailpipe format
---

# tailpipe format

List, show, and delete Tailpipe [formats](/docs/reference/config-files/format).

## Usage
```bash
tailpipe format list [args]
tailpipe format show format_type [args]
tailpipe format show format_type.format_name [args]
```

## Sub-Commands

| Command | Description
|-|-
| [list](#tailpipe-format-list) | List all formats.
| [show](#tailpipe-format-show)  | Show details of a format.
| [delete](#tailpipe-format-delete) | Delete a format.


## tailpipe format list
List all formats.

### Arguments

| Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output format: json, plain, pretty (default)

### Examples

List all formats.

```bash
tailpipe format list
```

List all formats in `JSON` format. 

```bash
tailpipe format list --output json
```

## tailpipe format show
Show details for a format.

Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output format: json, plain, pretty (default)


### Examples

Show format details in text format.

```bash
tailpipe format show nginx_access_log.combined
```

Show format type details in text format.

```bash
tailpipe format show nginx_access_log
```

Show details in JSON format.

```bash
tailpipe format show nginx_access_log.combined --output json
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



