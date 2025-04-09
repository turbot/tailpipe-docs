---
title: Create Custom Tables
---

# Custom Tables

Tailpipe [plugins](manage/plugin) define tables for common log sources and formats.  You don't need to define these tables; simply create one or more [partition](manage\partition) for the table and begin [collecting logs](manage/collection).

But what if your logs are not in a standard format, or are not currently supported by a plugin? No problem!  **Custom tables** enable you to collect data from arbitrary log files and other sources.

The process is straightforward:
- First [define a `format`](#define-the-format) that describes how to extract the fields from the source.
- Next, [define a `table`](#define-the-table) that transforms and maps the source `format` into a destination table structure.
- Create [one or more `partition`](#create-partitions) for your `table`, specifying a `source` from which to collect logs
- [Collect and query](#collect--query) as you would for any tailpipe table.

<!--  With Tailpipe, you can define and collect custom tables from any text-based format. 

This is useful if you need to analyze logs that have custom or proprietary formats, or to collect logs that do not have plugin support.

-->

## Define the Format

The [`format` block](/docs/reference/config-files/format) enables you to define source formats for custom tables. Formats describe the layout of the source data so that it can be collected into a table.

Format blocks have 2 labels:
- The [format type](/docs/reference/config-files/format#format-types). This can be a [core format type](/docs/reference/config-files/format#core-plugin-formats) (`grok`, `regex`, `delimited`, or `jsonl`) or any format type in any installed plugin.  
- A name for the format

For example, Steampipe plugin logs are syslog-style text files.   EVERY row has a timestamp, timezone, severity, and message.  Most log lines will also contain plugin-specific data - the plugin name, plugin severity, plugin timestamp in epoch seconds:

```
2025-04-08 15:16:35.733 UTC [DEBUG] steampipe-plugin-aws.plugin: [DEBUG] 1744125262935: retrying request Lambda/ListFunctions, attempt 8
2025-04-08 15:16:35.733 UTC [INFO]  steampipe-plugin-aws.plugin: [INFO]  1744125273258: BackoffDelay: attempt=8, retryTime=2m55.50675s, err=https response error StatusCode: 0, RequestID: , request send failed, Get "https://lambda.ap-northeast-1.amazonaws.com/2015-03-31/functions?MaxItems=10000": lookup lambda.ap-northeast-1.amazonaws.com on 192.168.1.254:53: read udp 192.168.1.204:57677->192.168.1.254:53: i/o timeout
2025-04-08 15:16:36.033 UTC [DEBUG] steampipe-plugin-aws.plugin: [DEBUG] 1744125262935: retrying request Lambda/ListFunctions, attempt 8
2025-04-08 15:16:36.033 UTC [INFO]  steampipe-plugin-aws.plugin: [INFO]  1744125273258: BackoffDelay: attempt=8, retryTime=2m16.14075s, err=https response error StatusCode: 0, RequestID: , request send failed, Get "https://lambda.ap-southeast-2.amazonaws.com/2015-03-31/functions?MaxItems=10000": lookup lambda.ap-southeast-2.amazonaws.com on 192.168.1.254:53: read udp 192.168.1.204:54718->192.168.1.254:53: i/o timeout
2025-04-08 15:16:38.463 UTC [DEBUG] steampipe-plugin-aws.plugin: [DEBUG] 1744125262935: retrying request Lambda/ListFunctions, attempt 8
2025-04-08 15:16:38.464 UTC [INFO]  steampipe-plugin-aws.plugin: [INFO]  1744125273258: BackoffDelay: attempt=8, retryTime=2m44.025s, err=https response error StatusCode: 0, RequestID: , request send failed, Get "https://lambda.ap-northeast-3.amazonaws.com/2015-03-31/functions?MaxItems=10000": lookup lambda.ap-northeast-3.amazonaws.com on 192.168.1.254:53: read udp 192.168.1.204:55845->192.168.1.254:53: i/o timeout
2025-04-08 15:16:58.442 UTC [INFO]  PluginManager Shutdown
2025-04-08 15:16:58.442 UTC [INFO]  PluginManager closing pool
2025-04-08 15:16:58.442 UTC [INFO]  Kill plugin hub.steampipe.io/plugins/turbot/terraform@latest (0xc00092e100)
2025-04-08 15:16:58.442 UTC [DEBUG] PluginManager killPlugin start
2025-04-08 15:16:58.442 UTC [INFO]  PluginManager killing plugin hub.steampipe.io/plugins/turbot/terraform@latest (81771)
2025-04-08 15:16:58.456 UTC [DEBUG] stdio: received EOF, stopping recv loop: err="rpc error: code = Unavailable desc = error reading from server: EOF"
2025-04-08 15:16:58.460 UTC [INFO]  plugin process exited: plugin=/Users/jsmyth/.steampipe/plugins/hub.steampipe.io/plugins/turbot/terraform@latest/steampipe-plugin-terraform.plugin id=81771
```

We can define a format that defines this structure.  In this example, we use the `grok` format type.

```hcl
format "grok" "steampipe_plugin" {
  layout = `%{TIMESTAMP_ISO8601:timestamp} %{WORD:timezone} \[%{LOGLEVEL:severity}\]\s+(?:%{NOTSPACE:plugin_name}: \[%{LOGLEVEL:plugin_severity}\]\s+%{NUMBER:plugin_timestamp}:\s+)?%{GREEDYDATA:message}`
}
```

> [!TIP]
> Use backticks (<code>`</code>) to delimit the <code>layout</code>.  Tailpipe treats anything in backticks as a non-interpolated string, so you don't have to escape quotes, backslashes, etc.


> [!TIP]
> Use the [Grok Debugger](https://grokdebugger.com/) to help create and test your grok expressions. 

## Define the Table

Custom tables are defined in a [`table` block](/docs/reference/config-files/table). Table blocks have a single label, which defines the name of the table. 

You may define the `format` of the source.  In this example, we will use [the format that that we created previously](#define-the-format).  By default, all the fields defined in `format` will be included as columns in the table.  If you want, you can use the `map_fields` to only include specific columns, however.

You may also use one or more [`column` definitions](/docs/reference/config-files/table#column-blocks) to map fields to map and transform data from the source.

In our example, the source format does not define a field named `tp_timestamp`.  Since ***`tp_timestamp` is a required column***,  we will add a `tp_timestamp` column and map the `timestamp` from the source.  Also, the source includes a `plugin_timestamp` but it is parsed as a number because it is epoch milliseconds.  We will transform it to a timestamp data type.


```hcl
table "steampipe_plugin" {
  format = format.grok.steampipe_plugin

  column "tp_timestamp" {
    source = "timestamp"
  }

  column "plugin_timestamp" {
    type      = "timestamp"
  }
}
```

## Create Partitions

Now that we have the `table` and `format` defined, we can create [a `partition`](/docs/reference/config-files/partition) and add [a `source`](/docs/reference/config-files/partition#source) from which to collect.  

The partition has two labels:
- The table name. The table name must match a table name for an installed plugin or a custom table. In this case, we will use the [table we created earlier](#define-the-table).
- A partition name. The partition name must be unique for all partitions in a given table (though different tables may use the same partition names).

You can use any [source](/docs/reference/config-files/partition#source) from any plugin.  In our example, we will use the `file` source from the `core` plugin that ships with Tailpipe.   Note that if your directory structure and/or file names are arranged by date/time, you should [specify the structure in the `file_layout`](/docs/reference/config-files/partition#file_layout) so that Tailpipe can use it in the collection state to optimize the collection process.

```hcl
partition "steampipe_plugin" "local" {
  source "file" {
   paths = ["/Users/jsmyth/.steampipe/logs"]
   file_layout = `plugin-%{YEAR:year}-%{MONTHNUM:month}-%{MONTHDAY:day}.log`
  }
}
```

## Collect & Query

You can [collect](/docs/collect/collect) custom logs with `tailpipe` collect, just like any other logs.

```bash
$ tailpipe collect steampipe_plugin

Collecting logs for steampipe_plugin.local from 2025-04-01 (initial collection, default 7 days)

Artifacts:
  Discovered: 1 
  Downloaded: 1 2.9MB
  Extracted:  1

Rows:
  Received: 15,386
  Enriched: 15,386
  Saved:    15,386

Files:
  Compacted: 2 => 1

Completed: 416ms
```

The next time you run `tailpipe query`, your table will be available to [query](/docs/query)!

```bash
MacBook-Pro-7:logs jsmyth$ tailpipe query
Welcome to Tailpipe v0.3.0-rc.0
For more information, type .help
> .inspect
Table              Plugin    
aws_cloudtrail_log aws@0.8.1 
steampipe_plugin  

> 
> select plugin_name, count(*) as count from steampipe_plugin group by plugin_name order by count desc;
+-----------------------------+-------+
| plugin_name                 | count |
+-----------------------------+-------+
| steampipe-plugin-aws.plugin | 14143 |
| <null>                      | 1243  |
+-----------------------------+-------+
> 
> select severity, count(*) as count from steampipe_plugin group by severity order by count desc;
+----------+-------+
| severity | count |
+----------+-------+
| INFO     | 14243 |
| DEBUG    | 1141  |
| ERROR    | 2     |
+----------+-------+

```



<!--  Where does this go?  why are there no descriptions?

## Common Fields

Tailpipe plugins populate a set of common fields. Some are mandatory, for example `tp_partition` and `tp_date`. Others, like `tp_source_ip` and `tp_ips`, are optional. Plugins map table-specific fields to these common fields when it is appropriate to do so. The AWS Cloudtrail plugin, for example, maps the value of the native field `SourceIPAddress` to the common field `tp_source_ip`. It also adds that address to the `tp_ips` array.

These mappings enable queries that correlate values across different logs. If you have collected both Cloudtrail and ALB logs, for example, you could query for source addresses that occur in both the `aws_cloudtrail_log` and `aws_alb_access_log` tables.

| Field Name | Type |
|------------|------|
| tp_akas | varchar[] |
| tp_date | date |
| tp_destination_ip | varchar |
| tp_domains | varchar[] |
| tp_emails | varchar[] |
| tp_id | varchar |
| tp_index | varchar |
| tp_ingest_timestamp | timestamp |
| tp_ips | varchar[] |
| tp_partition | varchar |
| tp_source_ip | varchar |
| tp_source_location | varchar |
| tp_source_name | varchar |
| tp_source_type | varchar |
| tp_table | varchar |
| tp_tags | varchar[] |
| tp_timestamp | timestamp |
| tp_usernames | varchar[] |


-->