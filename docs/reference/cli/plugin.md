---
title: tailpipe plugin
---

>[!NOTE]
> Cloned from Steampipe with minor variation: show is new, no service mode
> how much of the rest is applicable?


# tailpipe plugin
tailpipe plugin management

Plugins extend tailpipe to work with many different services and providers. Find plugins using the public registry at [hub.tailpipe.io](https://hub.tailpipe.io).


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
tailpipe plugin install aws@0.107.0
```

Install the latest version of a plugin matching a semver constraint:
```bash
tailpipe plugin install aws@^0.107
```

Note: if your semver constraint contain special characters you may need to quote it:
```bash
tailpipe plugin install "aws@>=0.100"
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
tailpipe plugin uninstall dmi/paper
```

Update all plugins to the latest in the installed stream:
```bash
tailpipe plugin update --all
```

Update the aws plugin to the latest version meeting the constraint:
```bash
tailpipe plugin update aws@^0.107
```

Update all plugins to the latest and hide the progress bar:
```bash
tailpipe plugin update --all --progress=false
```

Show details about a plugin:
```bash
tailpipe plugin show aws
```



