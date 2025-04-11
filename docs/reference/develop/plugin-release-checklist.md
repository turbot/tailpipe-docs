---
title: Plugin Release Checklist
sidebar_label: Plugin Release Checklist
---

# Plugin Release Checklist

All plugins are currently hosted on the [Tailpipe Hub](https://hub.tailpipe.io). If you want to contribute one — and we hope you do! — here are the most common things we ask contributors to check to prepare for the plugin's release. Feel free to tick the boxes as you go through the list!

- [Basic Configuration](#basic-configuration)
- [Configuration File](#configuration-file)
- [Credentials](#credentials)
- [Table and Column Names](#table-and-column-names)
- [Table and Column Descriptions](#table-and-column-descriptions)
- [Table and Column Design](#table-and-column-design)
- [Documentation](#documentation)
- [Final Review](#final-review)

## Basic Configuration

<input type="checkbox"/> <b>Repository name</b>

The repository name should use the format `tailpipe-plugin-<pluginName>`, e.g., `tailpipe-plugin-aws`, `tailpipe-plugin-azure`, `tailpipe-plugin-github`. The plugin name should be one word, so there are always three parts in the repository name.

<input type="checkbox"/> <b>Repository topics</b>

To help with discoverability in GitHub, the repository topics should include:
- duckdb
- sql
- tailpipe
- tailpipe-plugin

<input type="checkbox"/> <b>Repository website</b>

The repository website/homepage should link to the Hub site. The URL is composed of the GitHub organization and plugin name, for instance:
- https://github.com/turbot/tailpipe-plugin-aws results in https://hub.tailpipe.io/plugins/turbot/aws

<input type="checkbox"/> <b>Go version</b>

The Go version in `go.mod` and any workflows is 1.23.

<input type="checkbox"/> <b>.goreleaser.yml</b>

The `.goreleaser.yml` file uses the standard format, e.g., [AWS plugin .goreleaser.yml](https://github.com/turbot/tailpipe-plugin-aws/blob/main/.goreleaser.yml).

<input type="checkbox"/> <b>CHANGELOG</b>

A `CHANGELOG.md` is included and contains release notes for the upcoming version (typically v0.1.0).

<input type="checkbox"/> <b>License</b>

The plugin uses two licenses:
- Most of the plugin uses the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)
- Except for the docs which use the [CC BY-NC-ND License 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.en)

<input type="checkbox"/> <b>Makefile</b>

The `Makefile` file is present and builds to the correct plugin path.

## Credentials

<input type="checkbox"/> <b>Terraform compatibility</b>

If there's a Terraform provider for your API, the plugin supports the same credential methods as the provider.

<input type="checkbox"/> <b>Existing CLI credentials</b>

When there are commonly used CLI credentials, like `.aws/credentials`, the plugin works with them.

<input type="checkbox"/> <b>Expiry</b>

When credentials expire and the API's SDK does not automatically refresh them, the plugin alerts the user and tells them how to refresh.

<input type="checkbox"/> <b>Environment variables</b>

It's possible to set credentials using an environment variable if the API's SDK also supports using environment variables.

## Table and Column Names

<input type="checkbox"/> <b>Table names</b>

Table names use the format `<plugin>_<log type>`, e.g., `aws_cloudrail_log`, `aws_s3_server_access_log`, `azure_activity_log`, `github_audit_log`.

<input type="checkbox"/> <b>Column names</b>

Column names should use the lower snake case form of the log property. For instance, in `aws_cloudtrail_log`, the `userIdentity` property becomes the `user_identity` column.

## Table and Column Descriptions

<input type="checkbox"/> <b>Descriptions</b>

Every table and column has a description. These are consistent across tables. The default `tp_*` column descriptions should be overridden if there's additional log-specific information in the respective `tp_*` column.

## Table and Column Design

<input type="checkbox"/> <b>Row enrichment</b>

The table enriches the row with the following required [common columns](/docs/reference/config-files/table#common-columns):
- `tp_date` - The date the event was originally generated.
- `tp_id` - A unique identifier for the row. In Turbot plugins, it is typically set to an [xid](https://github.com/rs/xid).
- `tp_index` - The index used to partition the data, e.g., AWS account ID, GitHub organization, hostname.
- `tp_ingest_timestamp` - The timestamp when the event was ingested into the system.
- `tp_timestamp` -  The timestamp when the event was originally generated.

Additional common columns, like `tp_ips` and `tp_source_location`, should be added based on what is available in the log type.

<input type="checkbox"/> <b>Default file layout</b>

For each supported artifact source, the table defines a default `FileLayout` that uses the most common pattern. For instance, the `aws_cloudtrail_log` uses the standard AWS log format as the default `FileLayout` for the `aws_s3_bucket` source as part of its [GetSourceMetadata function](https://github.com/turbot/tailpipe-plugin-aws/blob/6b2620d49330aff84f8879e2740fabb39fc87b79/tables/cloudtrail_log/cloudtrail_log_table.go#L26-L28).

### Logging

<input type="checkbox"/> <b>Error info</b>

When the plugin returns an error, it includes the location and any related args, along with the error itself.

### Column Types

<input type="checkbox"/> <b>Struct vs. JSON</b>

Struct columns are preferred, so columns with nested properties should use the `STRUCT` type if the property structure is known; however, if a column has unknown properties, it should use the `JSON` type.

## Documentation

### Index Documentation

<input type="checkbox"/> <b>Front matter</b>

The index document contains a front matter block, like the one below:

```yml
---
organization: Turbot
category: ["public cloud"]
icon_url: "/images/plugins/turbot/aws.svg"
brand_color: "#FF9900"
display_name: Amazon Web Services
description: "Tailpipe plugin for collecting and querying various logs from AWS."
og_description: "Collect AWS logs and query them instantly with SQL! Open source CLI. No DB required."
---
```
<input type="checkbox"/> <b>Front matter: category</b>

The category is an appropriate choice from the list at [hub.tailpipe.io/plugins](https://hub.tailpipe.io/plugins).

<input type="checkbox"/> <b>Front matter: icon_url</b>

The icon URL is a link to an `.svg` file hosted on hub.tailpipe.io. Please request an icon through the [Turbot Community Slack](https://turbot.com/community/join) and a URL will be provided to use in this variable.

<input type="checkbox"/> <b>Front matter: brand color</b>

The color matches the provider's brand guidelines, typically stated on a page like [this one](https://developer.amazon.com/en-US/alexa/branding/alexa-guidelines/brand-guidelines/color) for Amazon Alexa.

<input type="checkbox"/> <b>Plugin description</b>

The description in `docs/index.md` is appropriate for the provider. The [AWS plugin](https://hub.tailpipe.io/plugins/turbot/aws), for example, uses:

```
AWS provides on-demand cloud computing platforms and APIs to authenticated customers on a metered pay-as-you-go basis.
```

The opening sentence of the Wikipedia page for the provider can be a good source of guidance here.

<input type="checkbox"/> <b>Powerpipe mods</b>

If a plugin has at least one table with a matching Detections mod, e.g., [AWS CloudTrail Log Detections](https://hub.powerpipe.io/mods/turbot/tailpipe-mod-aws-cloudtrail-log-detections), then the `Detections as Code with Powerpipe` section is added and links to the mod.

<input type="checkbox"/> <b>Connection credentials</b>

If a plugin defines a connection type, the plugin should:

- List connection arguments
- Link to provider documentation
- Explain how to use existing CLI creds when that's possible

### Table Documentation

<input type="checkbox"/> <b>Example usage</b>

Each table document shows a basic example of how to configure a partition with a connection (if applicable) and then collect log data from that partition.

<input type="checkbox"/> <b>Useful examples</b>

Each table document shows 3-5 <i>useful</i> examples that reflect real-world scenarios.

<input type="checkbox"/> <b>Column specificity</b>

All examples should specify columns and should usually include the log's timestamp and actor (if available) as the first column. Using `SELECT *` is generally not preferred as it can produce too much data to be helpful.

### Source Documentation

<input type="checkbox"/> <b>Example configurations</b>

Each source document shows 3-5 configuration examples that users can copy, modify, and use to collect logs easily.

<input type="checkbox"/> <b>Arguments</b>

All source arguments should be listed along with their types, descriptions, and if they're required.

<input type="checkbox"/> <b>Table defaults</b>

For each table that defines defaults for the source's arguments, a link should be added to the relevant table's `Source Defaults` section. For instance, the [aws_s3_bucket](https://hub.tailpipe.io/plugins/turbot/aws/sources/aws_s3_bucket#table-defaults) document links to [aws_cloudtrail_log - Source Defaults: aws_s3_bucket](https://hub.tailpipe.io/plugins/turbot/aws/tables/aws_cloudtrail_log#aws_s3_bucket).

## Final Review

<input type="checkbox"/> <b>Testing</b>

The plugin has been tested with real log data.

<input type="checkbox"/> <b>Matching query examples</b>

The example in `README.md` matches the one in `docs/index.md`.

<input type="checkbox"/> <b>Icon</b>

Each plugin has an icon shown on the Hub. Please request an icon through the [Turbot Community Slack](https://tailpipe.io/community/join).

<input type="checkbox"/> <b>Ease of first use</b>

The plugin really nails easy setup; there's a short path to a first successful query, and it runs quickly.

<input type="checkbox"/> <b>Pre-mortem</b>

You've considered and addressed reasons why this plugin could fail to delight its community.
