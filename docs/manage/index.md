---
title: Manage Tailpipe
---

# Manage Tailpipe

Tailpipe is simple to install, it's distributed as a single binary file that you can [download and run](/downloads). You use Tailpipe to collect logs, then query them. 

When you collect logs from a source, e.g. an S3 bucket containing Cloudtrail logs, you invoke a plugin that uses a connection to read the source and build a database table. The unit of collection is the partition:  a table may comprise one one or more partitions. The collection process writes to a hierarchy of folders and files called the hive, over which Tailpipe builds the database table that you query. The workspace, by default `~/.tailpipe/data/default`, defines the location of the hive.




