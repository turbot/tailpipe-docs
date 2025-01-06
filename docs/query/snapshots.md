---
title: Snapshots
---

# Snapshots

>[!NOTE]
> i think essentially all of this applies?


Tailpipe enables you to take **snapshots**.  A snapshot is a saved view of a benchmark run or dashboard that you can view as a [dashboard in Powerpipe](https://powerpipe.io/docs/run/dashboard)  All data and metadata for a snapshot is contained in a JSON file which can be saved and viewed locally in the Powerpipe dashboard or uploaded to [Turbot Pipes](https://turbot.com/pipes/docs).  Snapshots in Turbot Pipes may be shared with other Turbot Pipes users or made public (shared with anyone that has the link).

You can create Turbot Pipes snapshots directly from the Powerpipe CLI, however if you wish to subsequently [modify](https://turbot.com/pipes/docs/dashboards#managing-snapshots) them (add/remove tags, change visibility) or delete them, you must do so from the Turbot Pipes console. You may [browse the snapshot list](https://turbot.com/pipes/docs/dashboards#browsing-snapshots) in Turbot Pipes by clicking the **Snapshots** button on the top of your workspace's **Dashboards** page.


## Taking Snapshots

To upload snapshots to Turbot Pipes, you must either [log in via the `powerpipe login` command](https://powerpipe.io/docs/reference/cli/login) or create an [API token](https://turbot.com/pipes/docs/profile#tokens) and pass it via the [`--pipes-token`](/docs/reference/cli/overview#global-flags) flag or [`PIPES_TOKEN`](/docs/reference/env-vars/pipes_token) environment variable.

To take a snapshot and save it to [Turbot Pipes](https://turbot.com/pipes/docs), simply add the `--snapshot` flag to your command.  

```bash
powerpipe query run "select * from aws_cloudtrail_log order by tp_date desc limit 1000" --snapshot
```

```bash
powerpipe benchmark run cloudtrail_log_detections --snapshot
```


The `--snapshot` flag will create a snapshot with `workspace` visibility in your user workspace. A snapshot with `workspace` visibility is visible only to users that have access to the workspace in which the snapshot resides -- A user must be authenticated to Turbot Pipes with permissions on the workspace.

If you want to create a snapshot that can be shared with *anyone*, use the `--share` flag instead. This will create the snapshot with `anyone_with_link` visibility:

```bash
powerpipe benchmark run cloudtrail_log_detections --share
```


You can set a snapshot title in Turbot Pipes with the `--snapshot-title` argument.

```bash
powerpipe query run "select * from aws_cloudtrail_log order by tp_date desc limit 1000" --share --snapshot-title "Recent Cloudtrail log lines"
```

If you wish to save to the snapshot to a different workspace, such as an org workspace, you can use the `--snapshot-location` argument with `--share` or `--snapshot`:

```bash
powerpipe query run "select * from aws_cloudtrail_log order by tp_date desc limit 1000" --share --snapshot-location my-org/my-workspace

```

## Tagging Snapshots

You may want to tag your snapshots to make it easier to organize them.  You can use the `--snapshot-tag` argument to add a tag:

```bash
powerpipe benchmark run cloudtrail_log_detections --snapshot-tag:source:cloudtrail
```

Simply repeat the flag to add more than one tag:
```bash
powerpipe benchmark run cloudtrail_log_detections --snapshot-tag:source:cloudtrail --snapshot-tag:env:prod
```

## Saving Snapshots to Local Files

Turbot Pipes makes it easy to save and share your snapshots, however it is not strictly required;  You can save and view snapshots using only the CLI.  

You can specify a local path in the `--snapshot-location` argument or `TAILPIPE_SNAPSHOT_LOCATION` environment variable to save your snapshots to a directory in your filesystem:

```bash
powerpipe benchmark run cloudtrail_log_detections --snapshot-location .

steampipe query --snapshot --snapshot-location . "select * from aws_account"
```

You can also set `snapshot_location` in a [workspace](/docs/managing/workspaces) if you wish to make it the default location.

>[!NOTE]
> remaining conversion tbd, assuming this is on the right track



Alternatively, you can use the `--export` argument to export a query in the Steampipe snapshot format.  This will create a file with a `.sps` extension in the current directory:

```bash
steampipe query --export sps "select * from aws_account"
```

The `snapshot` export/output type is an alias for `sps`:

```bash
steampipe query --export snapshot "select * from aws_account"
```

To give the file a name, simply use `{filename}.sps`, for example:

```bash
steampipe query --export aws_accounts.sps "select * from aws_account"
```

Alternatively, you can write the steampipe snapshot to stdout with `--output sps`
```bash
steampipe query --output sps  "select * from aws_account" > aws_accounts.sps
```

or `--output snapshot`
```bash
steampipe query --output snapshot  "select * from aws_account" > aws_accounts.sps
```


## Controlling Output
When using `--share` or `--snapshot`, the output will include the URL to view the snapshot that you created in addition to the usual output:
```bash
Snapshot uploaded to https://pipes.turbot.com/user/costanza/workspace/vandelay/snapshot/snap_abcdefghij0123456789_asdfghjklqwertyuiopzxcvbn
```

You can use the `--progress=false` argument to suppress displaying the URL and other progress data.  This may be desirable when you are using an alternate output format, especially when piping the output to another command:

```bash
steampipe query --snapshot --output json  \
  --progress=false  "select * from aws_account" | jq
```
