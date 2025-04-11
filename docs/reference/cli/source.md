---
title: tailpipe source
---

# tailpipe source

List, show, and delete Tailpipe [sources](/docs/reference/config-files/source).

## Usage
```bash
tailpipe source list [args]
tailpipe source show source_type [args]


```

## Sub-Commands

| Command | Description
|-|-
| [list](#tailpipe-source-list) | List all sources.
| [show](#tailpipe-source-show)  | Show details of a source.



## tailpipe source list
List all sources.

### Arguments

| Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output source: json, text (default)

### Examples

List all sources.

```bash
tailpipe source list
```

List all sources in `JSON` source. 

```bash
tailpipe source list --output json
```

## tailpipe source show
Show details for a source.

Flag | Description
|-|-
|  `--help`      |  Show help.
|  `--output`    |  Output format: json, plain, pretty (default)


### Examples


Show source type details in text source.

```bash
tailpipe source show aws_s3_bucket
```

Show details in JSON source.

```bash
tailpipe source show aws_s3_bucket --output json
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



