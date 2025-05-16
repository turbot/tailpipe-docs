---
title: TAILPIPE_PLUGIN_MEMORY_MAX_MB
sidebar_label: TAILPIPE_PLUGIN_MEMORY_MAX_MB
---

# TAILPIPE_PLUGIN_MEMORY_MAX_MB

Set a soft memory limit for each plugin running within the `tailpipe` process. This value determines the target memory usage for an individual plugin execution context.

Setting a per-plugin memory limit helps prevent any single plugin from consuming excessive memory.

## Usage

Set a per-plugin memory soft limit to 512MB:
```bash
export TAILPIPE_PLUGIN_MEMORY_MAX_MB=512
```