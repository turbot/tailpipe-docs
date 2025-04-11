---
title:  workspace
---
# workspace 

<!--


A Tailpipe workspace is a profile that defines the environment in which Tailpipe operates.

Each workspace comprises:

- A single local workspace directory for Tailpipe data (Parquet) and metadata files

- Optionally, context-specific settings and options

You can use **workspaces** to keep independent sets of data. Tailpipe workspaces enable you to define multiple named configurations and easily switch among them using the `--workspace` argument or `TAILPIPE_WORKSPACE` environment variable.  If there is no `--workspace` argument or `TAILPIPE_WORKSPACE` environment variable, then the `default` workspace will be used.

Workspace configurations can be defined in any `.tpc` file in the `~/.tailpipe/config` directory, but by convention they are defined in `~/.tailpipe/config/workspaces.tpc` file. This file may contain multiple workspace definitions that can then be referenced by name.


-->

A Tailpipe `workspace` is a "profile" that provides a distinct database storage location for your collected logs and allows you to define options for running Tailpipe.  

```hcl
workspace "default" {
  log_level     = "info"
}

workspace "development" {
  log_level     = "debug"
  update_check  = false
}
```

Tailpipe workspaces allow you to define multiple named configurations and easily switch between them using the `--workspace` argument or `TAILPIPE_WORKSPACE` 
environment variable. 

```bash
tailpipe query --workspace my_tailpipe
tailpipe collect --workspace my_tailpipe
```


## Arguments

| Argument            |    Default  | Description
|---------------------|-------------|-----------------------------------------
| `log_level`         | off         | Set the logging output level
| `update_check`      | `true`      | Enable or disable automatic update checking.


Workspaces are defined using the `workspace` block in one or more Tailpipe config files.  You can define them in any configuration file (`*.tpc`) from your config directory (`~/.tailpipe/config` by default), but by convention they are usually written to `~/.tailpipe/config/workspaces.tpc`.

The workspace named `default` is special; If a workspace named `default` exists, it will be used whenever the `--workspace` argument is not passed to Tailpipe.  Creating a `default` workspace in `~/.tailpipe/config/workspaces.tpc` provides a way to set all defaults.

Note that the HCL arguments correspond to environment variables:

| Workspace Argument | Environment Variable             
|--------------------|-------------------------
| `log_level`        | [`TAILPIPE_LOG_LEVEL`](/docs/reference/env-vars/tailpipe_log_level)
| `update_check`     | [`TAILPIPE_UPDATE_CHECK`](/docs/reference/env-vars/tailpipe_update_check)
