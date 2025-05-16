---
title: TAILPIPE_TEMP_DIR_MAX_MB
sidebar_label: TAILPIPE_TEMP_DIR_MAX_MB
---

# TAILPIPE_TEMP_DIR_MAX_MB

Define the maximum amount of disk space (in MB) that `tailpipe` is allowed to use for temporary storage of intermediate data, such as JSONL-formatted files used during processing.

This limit applies to the `.tailpipe/internal` directory and ensures that large result sets or slow-consuming data pipelines do not exhaust available disk space.

Set `TAILPIPE_TEMP_DIR_MAX_MB` to `0` to disable the disk space limit.

## Usage

Limit temporary storage to 1GB:
```bash
export TAILPIPE_TEMP_DIR_MAX_MB=1024
```