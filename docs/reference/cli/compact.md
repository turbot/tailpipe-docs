---
title: tailpipe compact
---

Compact multiple parquet files per day to one per day.

# tailpipe compact

>[!NOTE]
> Given that collect's --compact defaults to true, why would I use this?

# Usage
```bash
 tailpipe compact [table] [table.partition] [args]
 ```

### Arguments

| Flag | Description
|-|-
| `--from string`    |  Compact days newer than a relative or absolute date.
| `--to string`      |  Compact days older than a relative or absolute date.
| `--output output`  |  Output format; one of: json, table (default text)
|  `--help`          |  Help for connect

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

Compact the metadata:

>[!NOTE]
> in scope?

```hcl
tailpipe compact --metadata
```

