---
title: Connection
---

# Connection

**Connections** provide *credentials* and *configuration options* to connect to external services.  Tailpipe connections are similar to connections in Steampipe and Flowpipe. 

```hcl
connection "aws" "aws_01" {
  profile = "aws_01"
}

connection "aws" "aws_03" {
  access_key = "AKIA4YFAKEKEYXTDS252"
  secret_key = "SH42YMW5p3EThisIsNotRealzTiEUwXN8BOIOF5J8m"
}

connection "gcp" "gcp_my_other_project" {
  project     = "my-other-project"
  credentials = "/home/me/my-service-account-creds.json"
}
```

A default connection, e.g. `connection.aws.default` always exists; it can be overridden in a `.tpc` file. Each plugin has its own default credential resolution. Tailpipe defines a [connection type](reference/config-files/connection) for each plugin.

