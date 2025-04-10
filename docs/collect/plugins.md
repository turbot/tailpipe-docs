---
title: Manage Plugins
---

# Manage Plugins

Tailpipe provides an integrated, standardized SQL interface for querying various sources of log data. It relies on **plugins** to define and implement tables for those logs. This approach decouples the Tailpipe core from provider-specific implementations, providing flexibility and extensibility.

Tailpipe plugins are packaged as Open Container Images (OCI) and stored in the [Tailpipe Hub registry](https://hub.tailpipe.io).  This Hub registry contains a curated set of plugins developed by and/or vetted by Turbot.  To install the latest version of a standard plugin, you can simply install it by name.

For example, to install the latest `aws` plugin:

```
$ tailpipe plugin install aws
```

This will download the latest `aws` plugin from the Hub registry.

## Installing a Specific Version

To install a specific version, simply specify the version tag after the plugin name, separated by `@` or `:`

For example, to install the 0.118.0 version of the aws plugin:

```
$ tailpipe plugin install aws@0.118.0
```

This will download the aws plugin version 0.118.0 (the one with the `0.118.0` tag) from the Hub registry.

## Installing from a SemVer Constraint

Plugins should follow [semantic versioning](https://semver.org/) guidelines, and they are tagged in the Hub registry with a **version tag** that specifies their *exact version* in the `major.minor.patch` format (e.g. `1.0.1`).

The intent of the version tag is that it is immutable - while it is technically possible to move the version tag to a different image version, this should not be done.

Installing with a semver constraint allows you to "lock" (or pin) to a specific set of releases that match the constraints.

If you install via `tailpipe plugin install aws@^1`, for example, `tailpipe plugin update` (and auto-updates) will only update to versions greater than `1.0.0` but less than `2.0.0`.

Supported semver constraint types:

**Wildcard Constraint**: This matches any version for a particular segment (Major, Minor, or Patch).
- `1.x.x` would match any version with a major segment of `1`.
- `1.2.x` would match any version with a major segment of `1` and a minor segment of `2`.

**Caret Constraint (^)**: This matches versions that do not modify the left-most non-zero digit.
- `^1.2.3` is the latest version greater than or equal to `1.2.3` but less than `2.0.0`.
- `^0.1.2` is the latest version greater than or equal to `0.1.2` but less than `0.2.0`.

**Tilde Constraint (~)**: This matches versions based on expression; if a minor segment is expressed, it locks to it; otherwise it locks to the major version.
- `~1` is the latest version greater than or equal to `1.0.0` but less than `2.0.0` (same as `1.x.x`).
- `~1.2` is the latest version greater than or equal to `1.2.0` but less than `1.3.0` (same as `1.2.x`).
- `~1.2.3` is the latest version greater than or equal to `1.2.3` but less than `1.3.0`.

**Range Constraint**: This specifies a range of versions using a hyphen.
- `1.2.3-1.2.5` would limit to latest available version of `1.2.3`,`1.2.4` or `1.2.5`.

**Other Constraints**:
- `>1.1.1` would match any version greater than `1.1.1`.
- `>=1.2.0` would match any version greater than or equal to `1.2.0`.

You can use the install command in the same way as a specific version with these constraints (`imagename@constraint`) syntax:

> Note: For some constraints using special characters `>`, `<`, `*` you may need to escape the characters `\>` or quote the string `tailpipe plugin install "aws@>0.118.0"` depending on your terminal.

- To install the latest version locked to a specific major version:
```bash
$ tailpipe plugin install aws@^2
# or
$ tailpipe plugin install aws@2.x.x
```

- To install the latest version locked to a specific minor version:
```bash
$ tailpipe plugin install aws@~2.1
# or
$ tailpipe plugin install aws@2.1.x
```

<!--  this has not been tested....

## Installing from Another Registry

Tailpipe plugins are packaged in OCI format and can be hosted and installed from any artifact repository or container registry that supports OCI V2 images. To install a plugin from a repository, specify the full path in the install command:

```
$ tailpipe plugin install us-docker.pkg.dev/myproject/myrepo/myplugin@mytag
```
-->

## Viewing Installed Plugins
You can list the installed plugins with the `tailpipe plugin list` command:

```hcl
$ tailpipe plugin list
INSTALLED                                       VERSION    PARTITIONS
hub.tailpipe.io/plugins/turbot/aws@latest       0.1.0      aws_cloudtrail_log.dev, aws_cloudtrail_log.prod
hub.tailpipe.io/plugins/turbot/core@latest      0.1.3
hub.tailpipe.io/plugins/turbot/gcp@latest       0.1.0      gcp_audit_log.dev, gcp_audit_log.staging
```

## Updating Plugins

To update a plugin to the latest version for a given stream, you can use the  `tailpipe plugin update` command:

```
tailpipe plugin update plugin_name[@stream]
```

The syntax and semantics are identical to the install command -  `tailpipe plugin update aws` will get the latest aws plugin, `tailpipe plugin update aws@1` will get the latest in the 1.x major stream, etc.


To update **all** plugins to the latest in the installed stream:
```bash
tailpipe plugin update --all
```


## Uninstalling Plugins
You can uninstall a plugin with the `tailpipe plugin uninstall` command:

```
tailpipe plugin uninstall [plugin]
```

## Tailpipe Plugin Registry Support Lifecycle

The Tailpipe Plugin Registry is committed to ensuring accessibility and stability for its users by maintaining versions of plugins for at least one year and preserving at least one version of each plugin. This practice ensures that users can access older versions of plugins if needed, providing a safety net for compatibility issues or preferences.
