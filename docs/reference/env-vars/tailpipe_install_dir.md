---
title: TAILPIPE_INSTALL_DIR
sidebar_label: TAILPIPE_INSTALL_DIR
---

# TAILPIPE_INSTALL_DIR
Sets the directory for the Tailpipe installation, in which the Tailpipe database, plugins, and supporting files can be found.

Tailpipe is distributed as a single binary - when you install Tailpipe, either via `brew install` or via the `curl` script, the `tailpipe` binary is installed into your path.  The first time that you run Tailpipe, it will download and install the embedded database, foreign data wrapper extension, and other required files.   By default, these files are installed to `~/.tailpipe`, however you can change this location with the `TAILPIPE_INSTALL_DIR` environment variable or the `--install-dir` command line argument.

Tailpipe will read the `TAILPIPE_INSTALL_DIR` variable each time it runs; if it's not set, Tailpipe will use the default path (`~/.tailpipe`). If you wish to **ALWAYS** run Tailpipe from the alternate path, you should set your environment variable in a way that will persist across sessions (in your `.profile` for example).

To install a new Tailpipe instance into an alternate path, simply specify the path in `TAILPIPE_INSTALL_DIR` and then run a `tailpipe` command (alternatively, use the `--install-dir` argument).  If the specified directory is empty or files are missing, Tailpipe will install and update the database and files to `TAILPIPE_INSTALL_DIR`.  If the directory does not exist, Tailpipe will create it.  

It is possible to have multiple, parallel tailpipe instances on a given machine using `TAILPIPE_INSTALL_DIR` as long as they are running on a different port.



## Usage 
Set the TAILPIPE_INSTALL_DIR to `~/mypath`.  You will likely want to set this in your `.profile`.
```bash
export TAILPIPE_INSTALL_DIR=~/mypath
```
