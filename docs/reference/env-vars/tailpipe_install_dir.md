---
title: TAILPIPE_INSTALL_DIR
sidebar_label: TAILPIPE_INSTALL_DIR
---

# TAILPIPE_INSTALL_DIR
Sets the directory for the Tailpipe installation, in which the Tailpipe database, plugins, and supporting files can be found.

Tailpipe is distributed as a single binary. When you install Tailpipe, either via `brew install` or via the `curl` script, the `tailpipe` binary is installed by default to `~/.tailpipe`, however you can change this location with the `TAILPIPE_INSTALL_DIR` environment variable or the `--install-dir` command line argument.

Tailpipe will read the `TAILPIPE_INSTALL_DIR` variable each time it runs; if it's not set, Tailpipe will use the default path (`~/.tailpipe`). If you wish to **ALWAYS** run Tailpipe from the alternate path, you should set your environment variable in a way that will persist across sessions (in your `.profile`, for example).

To install a new Tailpipe instance into an alternate path, simply specify the path in `TAILPIPE_INSTALL_DIR` and then run a `tailpipe` command (alternatively, use the `--install-dir` argument).  If the directory does not exist, Tailpipe will create it.  


## Usage 
Set the TAILPIPE_INSTALL_DIR to `~/mypath`.  You will likely want to set this in your `.profile`.
```bash
export TAILPIPE_INSTALL_DIR=~/mypath
```
