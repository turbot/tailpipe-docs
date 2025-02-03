---
title: Workspace
---

# Workspace

A Tailpipe workspace is a profile that defines the environment in which Tailpipe operates.

Each workspace comprises:

- A single local workspace directory for Tailpipe data (Parquet) and metadata files

- Optionally, context-specific settings and options

You can use **workspaces** to keep independent sets of data. Tailpipe workspaces enable you to define multiple named configurations and easily switch among them using the `--workspace` argument or `TAILPIPE_WORKSPACE` environment variable.  If there is no `--workspace` argument or `TAILPIPE_WORKSPACE` environment variable, then the `default` workspace will be used.

Workspace configurations can be defined in any `.tpc` file in the `~/.tailpipe/config` directory, but by convention they are defined in `~/.tailpipe/config/workspaces.tpc` file. This file may contain multiple workspace definitions that can then be referenced by name.

```hcl
workspace "default" {
  local = "~/.tailpipe/data/default"
}

workspace "dev" {
  local = "~/.tailpipe/data/dev"
}
```
