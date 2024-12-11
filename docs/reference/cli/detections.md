---
title: powerpipe detections
---


# powerpipe detections

List, view, and run powerpipe detections.

## Usage

```bash
powerpipe detections list [args]
powerpipe detections show [args]
powerpipe detections run [args]
```

## Sub-Commands

| Command | Description
|-|-
| [list](#powerpipe-detection-list) | List detections from the current mod and its direct dependents [[true?]].
| [run](#powerpipe-detection-run)  | Run a detection from the current mod or its direct dependents.
| [show](#powerpipe-detection) | Show details of a detection from the current mod or its direct dependents.

----
## powerpipe detections list
List dashboards from the current mod and its direct dependents.

### Examples


List dashboards:
```bash
powerpipe detections list
```

List all dashboards in `JSON` format:
```bash
powerpipe detections list --output json
```

List dashboards using settings from a workspace:
```bash
powerpipe detections list --workspace my_workspace
```
## powerpipe detections show
Show details of a detection from the current mod or its direct dependents.

### Examples

Show details of a single detection in the current mod:
```bash
powerpipe detections show account_report
# or
powerpipe detections show detection.account_report
```

Show details of a single detection in a direct dependency mod:
```bash
powerpipe detections show aws_insights.detection.account_report
```

Show details of a detection in `JSON` format:
```bash
powerpipe detections show account_report --output json
```

Show details of a detections using settings from a workspace:
```bash
powerpipe detections show account_report -workspace my_workspace
```

---


## powerpipe detections run
Run a detections from the current mod or its direct dependents.

### Arguments

| Flag | Description
|-|-
| `--arg string=string`           | Specify the value for a detections input. Multiple `--arg` arguments may be passed. 
| `--der-timeout int`       | Set the detections execution timeout, in seconds. The default is `0` (no timeout).
|  `--database`         | ***DEPRECATED - See [Setting the Database](/docs/build/mod-database) for the new syntax.***  Sets the [database that powerpipe will connect to](/docs/run#selecting-a-database). This defaults to the local Steampipe database, but can be any PostgreSQL, MySQL, DuckDB, or SQLite database.
|  `--export string`              | Export detections output to a file. You may export multiple output formats for a single detections run by entering multiple `--export` arguments. If a file path is specified as an argument, its type will be inferred by the suffix. Supported export formats are `none`, `pps` (`snapshot`)
|  `--input`                      | Enable/Disable interactive prompts for missing variables. To disable prompts and fail on missing variables, use  `--input=false`. This is useful when running from scripts. (default `true`)
|  `--max-parallel int`           | Set the maximum number of database connections to open. When running dashboards, powerpipe will attempt to run up to this many dashboards in parallel. See the `powerpipe_MAX_PARALLEL` environment variable documentation for details. (default `10`)
|  `--mod-install`                | Specify whether to install mod dependencies before running the detections (default `true`)
|  `--output string`              | Select the console output format. Defaults to text. Possible values are `none`, `pps` (`snapshot`)
|  `--pipes-host`                 | Sets the Turbot Pipes host used when connecting to Turbot Pipes workspaces. See  [PIPES_HOST](/docs/reference/env-vars/pipes_host) for details.
|  `--pipes-token`                | Sets the Turbot Pipes authentication token used when connecting to Turbot Pipes workspaces. See  [PIPES_TOKEN](/docs/reference/env-vars/pipes_token) for details.
|  `--progress`                   | Enable or disable progress information. By default, progress information is shown - set  `--progress=false` to hide the progress bar.
|  `--query-timeout int`          | The query timeout, in seconds. The default is `300`.
|  `--search-path strings`        | Set a comma-separated list of connections to use as a custom search path for the detections run.
|  `--search-path-prefix strings` | Set a comma-separated list of connections to use as a prefix to the current search path for the detections run.
|  `--share`                      | Create snapshot in Turbot Pipes with `anyone_with_link` visibility.
|  `--snapshot`                   | Create snapshot in Turbot Pipes with the default (`workspace`) visibility.
|  `--snapshot-location string`   |	The location to write snapshots - either a local file path or a Turbot Pipes workspace
|  `--snapshot-tag string=string` | Specify tags to set on the snapshot. Multiple `--snapshot-tag `arguments may be passed.
|  `--snapshot-title string=string` | The title to give a snapshot when uploading to Turbot Pipes.
| `--var string=string`           | Specify the value of a variable.  Multiple `--var` arguments may be passed. 
| `--var-file strings`            | Specify a `.ppvar` file containing variable values.



### Examples

Run a detections and output the snapshot data:
```bash
powerpipe detections run account_report
```

Run a detections and output the snapshot data to a file:
```bash
powerpipe detections run account_report > mysnap.pps
# or
powerpipe detections run account_report --export mysnap.pps
```


Run a detections and upload a snapshot with `workspace` visibility in your user workspace.
```bash
powerpipe detections run account_report --snapshot  
```

Run a detections and upload a snapshot with `anyone_with_link` visibility in your user workspace.
```bash
powerpipe detections run --share account_report 
```

Run a detections and save a [snapshot](/docs/run/snapshots/batch-snapshots), specifying inputs:

```bash
powerpipe detections run aws_insights.der.aws_vpc_detail \
  --snapshot \
  --der-input vpc_id=vpc-9d7ae1e7
```


Run a detections and upload a snapshot with `anyone_with_link` visibility to a specific workspace.
```bash
powerpipe detections run account_report --share  --snapshot-location vandelay-industries/latex 
```

Run a der, upload a snapshot with `workspace` visibility in your user workspace, and tag the snapshot:
```bash
powerpipe detections run account_report --snapshot --snapshot-tag env=local 
```

Run a detections against a pipes workspace:
```bash
powerpipe detections run account_report --workspace acme/anvils
```