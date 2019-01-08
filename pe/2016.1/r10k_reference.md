---
layout: default
title: "r10k subcommand reference"
canonical: "/pe/latest/r10k_reference.html"
description: "Subcommand reference for r10k, a Puppet code management tool."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[r10k_config]: ./r10k_config.html
[r10k_custom]: ./r10k_custom.html
[r10k_run]: ./r10k_run.html
[r10k_ref]: ./r10k_ref.html
[environ_dir]: {{puppet}}/environments_configuring.html

The following subcommands are available with `r10k`:

* [`deploy`](#deploy): Performs operations on environments.
* `help`: Shows the help page for r10k.
* [`puppetfile`](#puppetfile): Performs operations on a Puppetfile.
* `version` Prints the version of r10k you are using.

## First-level subcommand options

Each subcommand allows the following options:

* `--color`: Enables color coded log messages.
* `-h`, `--help`: Shows help for this command.
* `-t`, `--trace`: Displays stack traces on application crash.
* `-v`, `--verbose`: Sets log verbosity. Valid values: fatal, error, warn, notice, info, debug, debug1, debug2.

### `deploy`

The `r10k deploy` command accepts the following subcommands:

* `display`: Displays a list of environments in your deployment.
  * To display a list of modules in the Puppetfile, add the `-p` flag to the command.
  * To display detailed information, add `--detail` to the command.
  * To update the environment sources, add `--fetch` to the command. This allows you to check for missing environments.
* `environment`: Deploys environments and their dependent modules.
  * To update modules specified in the environment's Puppetfile, add the `-p` flag to the command.
* `module`: Deploys a module in all environments.
  * To update the modules in a particular environment, add the `-e` flag to the command.

See the [Deploying environments with r10k](./r10k_run.html) for examples of r10k deploy command use.

### `puppetfile`

Note that these subcommands must be run as the user with write access to that environment’s modules directory (or the moduledir directory, if you specified a different modules directory in the Puppetfile). These commands interact with the Puppetfile in the current working directory, so before running the subcommand, make sure you are in the directory of the Puppetfile you want to use.

The `r10k puppetfile` command accepts the following subcommands:

* `check`: Verifies the Puppetfile syntax is correct.
* `install`: Installs all modules from a Puppetfile.
  * To set a custom puppetfile path, add the `--puppetfile` flag to the command.
  * To set a custom moduledir location, add the `--moduledir` flag to the command.
* `purge`: Purges unmanaged modules from a Puppetfile managed directory.
