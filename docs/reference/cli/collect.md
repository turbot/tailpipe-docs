---
title: tailpipe collect
---

# tailpipe collect

[Run collection](/docs/collect/collect).


To improve the first-run experience for collection, Tailpipe will only collect the last 7 days during the [initial collection](/docs/collect/collect#initial-collection) (though you can override this behavior with the `--from` and `--to` arguments).  Subsequent collection runs occur chronologically, resuming from the last collection by default, so there are no time gaps while the data is being collected.



## Usage
```bash
 tailpipe collect [table|table.partition] [flags]
 ```

### Arguments

| Flag | Description
|-|-
|  `--compact`       | Compact the Parquet files after collection (default true)
|  `--from string`   | Collect days newer than a relative or absolute date.
|  `--help`          |  Help for collect
| `--overwrite`      | Overwrite existing data for the specified time range
| `--progress`       | Show active progress of collection, set to `false` to disable (default `true`)
| `--to string`      | Collect days older than a relative or absolute date (use with `--from` for time ranges)



## Examples

Collect everything.

```bash
tailpipe collect
```

Collect all partitions in the `aws_cloudtrail_log` table.

```bash
tailpipe collect aws_cloudtrail_log
```

Collect one partition in the `aws_cloudtrail_log` table.

```bash
tailpipe collect aws_cloudtrail_log.dev
```

Collect all partitions in the `aws_cloudtrail_log` table for the last 45 days.

```bash
tailpipe collect aws_cloudtrail_log --from T-45d
```

Collect all partitions in the `aws_cloudtrail_log` between January and June.

```bash
tailpipe collect aws_cloudtrail_log --from 1/1/2024 --to 6/30/2024
```

Collect and overwrite existing data for a specific time range.

```bash
tailpipe collect aws_cloudtrail_log --from 1/1/2024 --to 6/30/2024 --overwrite
```

Collect all partitions in the `aws_cloudtrail_log` table and output JSON.

```bash
tailpipe collect aws_cloudtrail_log --output json
```

Collect everything, don't display progress.

```bash
tailpipe collect --progress=false
```
