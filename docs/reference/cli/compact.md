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

