---
title: tailpipe collect
---

# tailpipe collect

Run a [collection](TBD).


## Usage
```bash
tailpipe collect [table.partition] [flags]
```
### Arguments

| Flag | Description
|-|-
|  `--help`      | Help for collect
|  `--compact`   | Compact the parquet files after collection (default true)

## Examples

Collect the `trail2` partition of the `aws_cloudtrail_log` table.

```
tailpipe collect aws_cloudtrail_log.trail2
```

