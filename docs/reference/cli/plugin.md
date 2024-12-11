---
title: tailpipe plugin
sidebar_label: tailpipe plugin
---

# tailpipe plugin
Tailpipe plugin management.

Plugins extend Tailpipe to work with many different log sources. Find plugins using the public registry at [hub.tailpipe.io](https://hub.tailpipe.io).


## Usage
```bash
tailpipe plugin [command]
```

## Argument Reference
| Argument |Type | Required? | Description
|-|-|-|-
| `children` | List |  Optional| An ordered list of `control` and/or `benchmark` references that are members (direct descendants) of the benchmark.
| `description` | String |  Optional| A description of the benchmark
| `documentation` | String (Markdown)| Optional | A markdown string containing a long form description, used as documentation for the mod on hub.powerpipe.io. 
| `tags` | Map | Optional | A map of key:value metadata for the benchmark, used to categorize, search, and filter.  The structure is up to the mod author and varies by benchmark and provider. 
| `title` | String | Optional | A display title for the benchmark

## Available Commands:

| Command | Description
|-|-
| `install`     | Install one or more plugins
| `list`        | List currently installed plugins
| `uninstall`   | Uninstall a plugin
| `update `     | Update one or more plugins

<table>
  <thead>
    <tr>
      <th nowrap="true">Flag</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td nowrap="true"><inlineCode>--all</inlineCode></td>
      <td>Applies only to <inlineCode>plugin update</inlineCode>, updates ALL installed plugins.</td>
    </tr>
    <tr>
      <td nowrap="true"><inlineCode>--progress</inlineCode></td>
      <td>Enable or disable progress information. By default, progress information is shown - set <inlineCode>--progress=false</inlineCode> to hide the progress bar. Applies only to <inlineCode>plugin install</inlineCode> and <inlineCode>plugin update</inlineCode>.</td>
    </tr>
      <tr>
      <td nowrap="true"><inlineCode>--skip-config </inlineCode></td>
      <td>Applies only to <inlineCode>plugin install</inlineCode>,  skip creating the default config file for plugin.</td>
    </tr>
  </tbody>
</table>

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

Install all missing plugins that specified in configuration files. Do not download their default configuration files:

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
