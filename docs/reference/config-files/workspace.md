---
title:  workspace
---
# workspace 

A Tailpipe `workspace` is a "profile" that provides a distinct filesystem location for your collected logs and allows you to define options for running Tailpipe.  

```hcl
workspace "default" {
  log_level     = "info"
}

workspace "development" {
  log_level     = "debug"
  update_check  = false
}
```

You can use workspaces to keep independent sets of data. Each workspace maintains its own [data files](/docs/collect/configure#hive-partitioning) written to `$TAILPIPE_INSTALL_DIR/data/{workspace name}` (e.g. `~/.tailpipe/data/default`).

Tailpipe workspaces allow you to define multiple named configurations and easily switch between them using the `--workspace` argument or `TAILPIPE_WORKSPACE` environment variable. 

```bash
tailpipe collect --workspace development
tailpipe query --workspace development
```

## Arguments

| Argument            |    Default  | Description
|---------------------|-------------|-----------------------------------------
| `log_level`         | off         | Set the logging output level
| `update_check`      | `true`      | Enable or disable automatic update checking.
| `memory_max_mb`     | `32768`     | Caps CLI memory usage and determines worker count (default 32GB).
| `plugin_max_memory_mb` | `32768`  | Sets soft memory cap per plugin (default 32GB).
| `max_temp_dir_mb`   | `262144`    | Limits JSONL temp file size on disk (default 256GB).


Workspaces are defined using the `workspace` block in one or more Tailpipe config files.  You can define them in any configuration file (`*.tpc`) from your config directory (`~/.tailpipe/config` by default), but by convention, they are usually written to `~/.tailpipe/config/workspaces.tpc`.

The workspace named `default` is special; If a workspace named `default` exists, it will be used whenever the `--workspace` argument is not passed to Tailpipe.  Creating a `default` workspace in `~/.tailpipe/config/workspaces.tpc` provides a way to set all defaults.

Note that the HCL arguments correspond to environment variables:

| Workspace Argument | Environment Variable             
|--------------------|-------------------------
| `log_level`        | [`TAILPIPE_LOG_LEVEL`](/docs/reference/env-vars/tailpipe_log_level)
| `update_check`     | [`TAILPIPE_UPDATE_CHECK`](/docs/reference/env-vars/tailpipe_update_check)
| `memory_max_mb`    | [`TAILPIPE_MEMORY_MAX_MB`](/docs/reference/env-vars/tailpipe_memory_max_mb)
| `plugin_max_memory_mb` | [`TAILPIPE_PLUGIN_MEMORY_MAX_MB`](/docs/reference/env-vars/tailpipe_plugin_memory_max_mb)
| `max_temp_dir_mb`  | [`TAILPIPE_TEMP_DIR_MAX_MB`](/docs/reference/env-vars/tailpipe_temp_dir_max_mb)
