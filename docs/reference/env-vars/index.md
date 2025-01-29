---
title:  Environment Variables
---

# Environment Variables

Tailpipe supports environment variables to allow you to change its default behavior.  These are optional settings - You are not required to set any environment variables.

Note that plugins may also support environment variables, but these are plugin-specific - refer to your plugin's documentation on the [Tailpipe Hub](https://hub.tailpipe.io/) for details.

## Tailpipe Environment Variables


| Command | Default | Description
|-|-|-
| [TAILPIPE_INSTALL_DIR](reference/env-vars/tailpipe_install_dir)| `~/.tailpipe` | The directory in which the Steampipe database, plugins, and supporting files can be found.
| [TAILPIPE_LOG_LEVEL](reference/env-vars/tailpipe_log)  | `warn` | Set the logging output level.
| [TAILPIPE_MEMORY_MAX_MB](reference/env-vars/tailpipe_memory_max_mb)| `1024` | Set a soft memory limit for the `tailpipe` process.
 |[TAILPIPE_PLUGIN_MEMORY_MAX_MB](reference/env-vars/tailpipe_plugin_memory_max_mb)| `1024` | Set a default memory soft limit for each plugin process.
| [TAILPIPE_QUERY_TIMEOUT](reference/env-vars/tailpipe_query_timeout)  |  `240` for detections, unlimited in all other cases. | Set the amount of time to wait for a query to complete before 
| [TAILPIPE_UPDATE_CHECK](reference/env-vars/tailpipe_update_check)| `true` | Enable/disable automatic update checking.
| [TAILPIPE_WORKSPACE](reference/env-vars/tailpipe_workspace)  | `default` | Set the Tailpipe workspace.
