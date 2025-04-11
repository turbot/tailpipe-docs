---
title: Configuration Files
---

# Configuration Files

Configuration resources like [partitions](/docs/reference/config-files/connection), [workspaces](/docs/reference/config-files/workspace), and [connections](/docs/reference/config-files/connection) are defined using HCL in one or more Tailpipe config (`.tpc`) files.  

Tailpipe will read *all* files with a `.tpc` extension from the `config` directory in your  [TAILPIPE_INSTALL_DIR](/docs/reference/env-vars/tailpipe_install_dir) (`~/.tailpipe/config` by default), so you are free to arrange your config files as you please.  A common convention, however, is to create a file per plugin for all the resources that pertain to it.  [Workspaces](/docs/reference/config-files/connection) are usually defined in a separate `workspaces.tpc` file.

You can override the configuration file location for a command with the [`--config-path` global argument](http://localhost:3000/docs/reference/cli#global-flags).