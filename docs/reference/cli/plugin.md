---
title: tailpipe plugin
---

# tailpipe plugin

Tailpipe plugin management. Plugins extend tailpipe to work with many different services and providers. Find plugins using the public registry at [hub.tailpipe.io](https://hub.tailpipe.io).


## Usage
```bash
tailpipe plugin [command]
```

## Available Commands:

| Command | Description
|-|-
| `install`     | Install one or more plugins
| `list`        | List currently installed plugins
| `show`        | Show details about an installed plugin
| `uninstall`   | Uninstall a plugin
| `update`     | Update one or more plugins



| Flag | Description
|--|--
| `--all`            | Applies only to `plugin update`, updates ALL installed plugins. 
| `--progress`       | Enable or disable progress information. By default, progress information is shown - set `--progress=false` to hide the progress bar. Applies only to `plugin install` and `plugin update`. 
| `--skip-config`    | Applies only to `plugin install`, skip creating the default config file for plugin. 


## Examples

Install or update a plugin:
```bash
tailpipe plugin install aws
```

Install a specific version of a plugin:
```bash
tailpipe plugin install aws@0.2.0
```

Install the latest version of a plugin matching a semver constraint:
```bash
tailpipe plugin install aws@^0.2
```

Note: if your semver constraint contain special characters you may need to quote it:
```bash
tailpipe plugin install "aws@>=0.2"
```

Install all missing plugins specified in configuration files. Do not download their default configuration files:

```bash
tailpipe plugin install --skip-config
```

List installed plugins:
```bash
tailpipe plugin list
```

Uninstall a plugin:
```bash
tailpipe plugin uninstall aws
```

Update all plugins to the latest in the installed stream:
```bash
tailpipe plugin update --all
```

Update the aws plugin to the latest version meeting the constraint:
```bash
tailpipe plugin update aws@^0.2
```

Update all plugins to the latest and hide the progress bar:
```bash
tailpipe plugin update --all --progress=false
```

Show details about a plugin:
```bash
tailpipe plugin show aws
```



## Global Flags

<table>
  <tr> 
    <th> Flag </th> 
    <th> Description </th> 
  </tr>

  <tr> 
    <td nowrap="true"> `--config-path`</td> 
    <td>  
    Sets the search path for <a href = "/docs/reference/config-files">configuration files</a>. This argument accepts a colon-separated list of directories.  All  configuration files (`*.tpc`) will be loaded from each path, with decreasing precedence.  The default is `.:$TAILPIPE_INSTALL_DIR/config` (`.:~/.tailpipe/config`).  This allows you to manage your <a href="/docs/reference/config-files/workspace"> workspaces </a> and <a href="/docs/reference/config-files/connection">connections</a> centrally in the `~/.tailpipe/config` directory, but override them in the working directory / mod location if desired.
    </td> 
  </tr>


  <tr> 
    <td nowrap="true"> `--workspace	`  </td> 
    <td>  Sets the Tailpipe workspace profile. If not specified, the default workspace will be used if it exists. See <a href="/docs/reference/config-files/workspace">workspace</a> for details. </td> 
  </tr>

</table>