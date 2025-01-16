---
title:  connection
---


# connection 

The `connection` block governs how Tailpipe authenticates to log sources. Arguments are plugin-specific, and they are used to specify credentials, accounts, and other options.  The [Tailpipe Hub](https://hub.tailpipe.io/plugins) provides detailed information about the arguments for each plugin. 

For example, you can connect to AWS sources with a profile:

```hcl
connection "aws" "account_a" {
  profile = "account_a"
  regions = ["*"]
}
```

```hcl
connection "aws" "account_b" {
  profile = "account_a"
  default_region = "us-east-1"
}
```

Or with an access key:

```hcl
connection "aws_account_a" {
  access_key     = "ASIA3ODZSWFYSN2PFHPJ"
  secret_key     = "gMCYsoGqjfThisISNotARealKeyVVhh"
  session_token  = "FwoGZXIvYXdzEJv//////////wEaDINJ"
  default_region = "us-east-1"
}
```

Or with other methods (including environment variables) documented on the [plugin page](https://hub-tailpipe-io.vercel.app/plugins/turbot/aws).

