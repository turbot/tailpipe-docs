---
title: Batch Queries
---

>[!NOTE]
> cloned from tailpipe, with minor variation. how much is applicable?

# Batch Queries

Tailpipe queries can provide valuable insight into your logs, and the interactive client is a powerful tool for ad hoc queries and exploration.  Often, however, you will write a query that you will want to re-run in the future, either manually or perhaps as a cron job.  Tailpipe allows you to save your query to a file, and pass the file into the `tailpipe query` command.

For example, lets create a query to find S3 buckets where versioning is not enabled.  Paste the following snippet into a file named `cloudtrail_event.sql`:

```sql
select
  aws_region,
  event_type
from
  aws_cloudtrail_log
where
  aws_region = 'us-east-1'
```

We can now run the query by passing the file name to `tailpipe query`
```bash
tailpipe query cloudtrail_event.sql
```

You can even run multiple sql files by passing a glob or a space separated list of file names to the command:
```bash
tailpipe query *.sql
```

## Query output formats
By default, the output format is `table`, which provides a tabular, human-readable view:
```
+-----------------------+-------------------+
|        aws_region     |  event_type       |
+-----------------------+-------------------+
| us-east-2             | AwsApiCall        |
| us-east-2             | AwsServiceEvent   |
| vpc-0bf2ca1f6a9319eea | 172.16.0.0/16     |
+-----------------------+-------------------+
```
  
You can use the `--output` argument to output in a different format.  To print your output to json, specify `--output json`:

```
$ tailpipe query "select aws_region, event_type from aws_cloudtrail_log" --output json
[
 {
  "aws_region": "us-east-1",
  "event_type": "AWSAPICall"
 },
 {
  "aws_region": "us-east-1",
  "event_type": "AWSServiceEvent"
 },

]

```

To print your output to csv, specify `--output csv`:

```
$ tailpipe query "select aws_region, event_type from aws_cloudtrail_log" --output csv
aws_region,event_type
us-east-1,AWSApiCall
us-east-1,AWSServiceEvent
```

Redirecting the output to CSV is common way to export data for use in other tools, such as Excel:

```
tailpipe query "select aws_region, event_type from aws_cloudtrail_log" --output json" --output csv > cloudtrail_events.csv
```


To use a different delimiter, you can specify the `--separator` argument.  For example, to print to a pipe-separated format:

```
$ tailpipe query "select aws_region, event_type from aws_cloudtrail_log" --output csv --separator '|'
aws_region|event_type
us-east-1|AWSApiCall
us-east-1|AWSServiceEvent
```
