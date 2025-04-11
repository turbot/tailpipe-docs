---
title: Tailpipe CLI
---

# Tailpipe CLI

## Sub-Commands

| Command | Description
|-|-
| [tailpipe collect](/docs/reference/cli/collect)   | Run a collection
| [tailpipe compact](/docs/reference/cli/compact)   | Compact multiple parquet files per day to one per day
| [tailpipe connect](/docs/reference/cli/connect)   | Return a connection string for a database
| [tailpipe help](/docs/reference/cli/help)         | Help about any command
| [tailpipe partition](/docs/reference/cli/partition)     | List, show, and delete Tailpipe partitions
| [tailpipe plugin](/docs/reference/cli/plugin)     | Tailpipe plugin management
| [tailpipe query](/docs/reference/cli/query)       | Execute a query against the workspace database
| [tailpipe source](/docs/reference/cli/source)       | List and show Tailpipe sources
| [tailpipe table](/docs/reference/cli/table)       | List and show Tailpipe tables


## Global Arguments

<table>
  <tr> 
    <th> Argument </th> 
    <th> Description </th> 
  </tr>

  <tr> 
    <td nowrap="true"> `--config-path`</td> 
    <td>  
    Sets the location to search for <a href = "/docs/reference/config-files/">configuration files</a>. The default is `$TAILPIPE_INSTALL_DIR/config`, eg `~/.tailpipe/config`.
    </td> 
  </tr>


  <tr> 
    <td nowrap="true"> `--workspace	`  </td> 
    <td>  Sets the Tailpipe workspace profile. If not specified, the default workspace will be used if it exists. See <a href="/docs/reference/config-files/workspace">workspace</a> for details. </td> 
  </tr>

</table>


## Exit Codes

|  Value  |   Name                                | Description
|---------|---------------------------------------|----------------------------------------
|   **0** | `ExitCodeSuccessful`                  | Tailpipe ran successfully
|   **1** | `ExitCodeExecutionPaused`             | Tailpipe ran without errors but paused waiting input
|   **2** | `ExitCodeExecutionFailed`             | Tailpipe completed with one or more errors
|   **3** | `ExitCodeExecutionCancelled`          | The Tailpipe command was canceelled by user request
|  **61** | `ExitCodeModInitFailed`               | Mod init failed
|  **62** | `ExitCodeModInstallFailed`            | Mod install failed
| **250** | `ExitCodeInitializationFailed`        | Initialization failed
| **251** | `ExitCodeBindPortUnavailable`         | Network port binding failed
| **252** | `ExitCodeNoModFile`                   | The command requires a mod, but no mod file was found
| **253** | `ExitCodeFileSystemAccessFailure`     | File system access failed
| **254** | `ExitCodeInsufficientOrWrongInputs`   | Runtime error - insufficient or incorrect input
| **255** | `ExitCodeUnknownErrorPanic`           | Runtime error - an unknown panic occurred
