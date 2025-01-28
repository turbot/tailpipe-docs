---
title:  workspace
---
# workspace 


A Tailpipe `workspace` is a "profile" that allows you to define options for running Tailpipe.  

```hcl

workspace "local_01" {
  memory_max_mb = 2048
}
```

Tailpipe workspaces allow you to define multiple named configurations and easily switch between them using the `--workspace` argument or `TAILPIPE_WORKSPACE` 
environment variable. 

```bash
tailpipe server --workspace my_tailpipe
```

To learn more, see **[Managing Workspaces →](/docs/manage/workspace)**


## Arguments

| Argument            |    Default  | Description
|---------------------|-----------------------------------------------|-----------------------------------------
| `log_level`         | off                          | Set the logging output level
| `memory_max_mb`     | `1024`                       | Set a memory soft limit for the tailpipe process. Set to 0 to disable the memory limit. This can also be set via the TAILPIPE_MEMORY_MAX_MB environment variable.
| `update_check`      | `true`                       | Enable or disable automatic update checking.


Workspaces are defined using the `workspace` block in one or more Tailpipe config files.  Tailpipe will load ALL configuration files (`*.tpc`) from every directory in the [configuration search path](/docs/reference/env-vars/tailpipe_config_path), with decreasing precedence. The set of workspaces is the union of all workspaces defined in these directories.  

The workspace named `default` is special; If a workspace named `default` exists, it will be used whenever the `--workspace` argument is not passed to Tailpipe.  Creating a `default` workspace in `~/.tailpipe/config/workspaces.tpc` provides a way to set all defaults.


Note that the HCL argument names are the same as the equivalent CLI argument names,
except using an underscore in place of a dash:

| Workspace Argument | Environment Variable    | Argument             
|--------------------|-------------------------|----------------------
| `log_level`        | `TAILPIPE_LOG_LEVEL`    |
| `memory_max_mb`    | `TAILPIPE_MEMORY_MAX_MB`|
| `update_check`     | `TAILPIPE_UPDATE_CHECK` | 

## Examples


```hcl

workspace "local_01" {
}

workspace "local_02" {
}

```