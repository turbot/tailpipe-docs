---
title: connection
---

# Connection

The `connection` block governs how Tailpipe authenticates to log sources. Arguments are plugin-specific, and they are used to specify credentials, accounts, and other options.  The [Tailpipe Hub](https://hub.tailpipe.io/plugins) provides detailed information about the arguments for each plugin. 

Examples:

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

Each connection has a type label and a name. There is a type for each service: [aws](/docs/reference/config-files/connection/aws), [azure](/docs/reference/config-files/connection/azure), [gcp](/docs/reference/config-files/connection/gcp), [pipes](/docs/reference/config-files/pipes), etc.

### Default connections

Tailpipe also creates a single **implicit default** connection for each connection type. The connection is named `default`, eg `connection.aws.default`, `connection.azure.defaut`, etc. The intent of the default connection is that Tailpipe can "just work" with your existing setup, and is similar to the default connection that Steampipe creates for each plugin. For example, the AWS default connection will use the standard AWS SDK connection resolution mechanism to create a Tailpipe connection that will use the same connection that the `aws` CLI would use.

You can override the default by simply creating a connection for that type that is named `default`:

```hcl
connection "aws" "default" {
  profile = "aws_01"
}
```
