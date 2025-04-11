---
title: Snapshots
---

# Snapshots

Tailpipe enables you to take **snapshots**.  A snapshot is a saved view of a benchmark run or dashboard that you can view as a [dashboard in Powerpipe](https://powerpipe.io/docs/run/dashboard)  All data and metadata for a snapshot is contained in a JSON file which can be saved and viewed locally in the Powerpipe dashboard or uploaded to [Turbot Pipes](https://turbot.com/pipes/docs).  Snapshots in Turbot Pipes may be shared with other Turbot Pipes users or made public (shared with anyone who has the link).

You can create Turbot Pipes snapshots directly from the Powerpipe CLI, but if you wish to subsequently [modify](https://turbot.com/pipes/docs/dashboards#managing-snapshots) them (add/remove tags, change visibility) or delete them, you must do so from the Turbot Pipes console. You may [browse the snapshot list](https://turbot.com/pipes/docs/dashboards#browsing-snapshots) in Turbot Pipes by clicking the **Snapshots** button on the top of your workspace's **Dashboards** page.


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

If you wish to save the snapshot to a different workspace, such as an org workspace, you can use the `--snapshot-location` argument with `--share` or `--snapshot`:

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

Turbot Pipes makes it easy to save and share your snapshots, but you can also save and view snapshots using only the CLI.  

You can specify a local path in the `--snapshot-location` argument or `TAILPIPE_SNAPSHOT_LOCATION` environment variable to save your snapshots to a directory in your filesystem:

```bash
powerpipe benchmark run cloudtrail_log_detections --snapshot-location .
```
