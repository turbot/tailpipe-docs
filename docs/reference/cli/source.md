---
title: tailpipe source
---

# tailpipe source

List, show, and delete Tailpipe [sources](/docs/reference/config-files/partition#source).

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
|  `--output`    | Output format: `json`, `plain`, `pretty` (default) 

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
|  `--output`    |  Output format: `json`, `plain`, `pretty` (default) 


### Examples


Show source type details.

```bash
tailpipe source show aws_s3_bucket
```

Show source type details in JSON.

```bash
tailpipe source show aws_s3_bucket --output json
```
