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


This pages lists the commands available for use with r10k.

## r10k commands

Run these commands with `r10k`. For example:

`r10k deploy`

r10k command   | Action
----------------|:---------------:|
`deploy` | Performs operations on environments. Accepts [deploy subcommands](#deploy) listed below.
`help`| Displays the r10k help page in the terminal.
`puppetfile` | Performs operations on a Puppetfile. Accepts [puppetfile subcommands](#puppetfile).
`version` | Displays your r10k version in the terminal

These commands accept the following options:

r10k command options   | Action
----------------|:---------------:|
`--color` | Enables color coded log messages.
`-h`, `--help`| Shows help for this command.
`-t`, `--trace` | Displays stack traces on application crash.
`-v`, `--verbose` | Sets log verbosity. Valid values: fatal, error, warn, notice, info, debug, debug1, debug2.

### `deploy`

You can use the following subcommands with the `r10k deploy` command.

`deploy` subcommand   | Action
----------------|:---------------:
`display` | Displays a list of environments in your deployment.
`display -p` | Displays a list of modules in the Puppetfile.
`display --detail`| Displays detailed information.
`display --fetch` | Update the environment sources. Allows you to check for missing environments.
`environment`| Deploys environments and their specified modules.
`environment -p`| Updates modules specified in the environment's Puppetfile.
`module` | Deploys a module in all environments
`module -e` | Updates all modules in a particular environment

See the [deploying environments with r10k](./r10k_run.html) documentation for examples of `r10k deploy` command use.

### `puppetfile`

You can use the following subcommands with the `r10k puppetfile` command. For example:

``` bash
r10k puppetfile install`
```

`puppetfile` subcommand   | Action
----------------|:---------------:
`check` | Verifies the Puppetfile syntax is correct.
`install` | Installs all modules from a Puppetfile.
`install --puppetfile`| Installs modules from a custom Puppetfile path.
`install --moduledir` | Installs modules from a Puppetfile to a custom module directory location.
`purge`| Purges unmanaged modules from a Puppetfile managed directory.

Note that these subcommands must be run as the user with write access to that environment’s modules directory (or the moduledir directory, if you specified a different modules directory in the Puppetfile). These commands interact with the Puppetfile in the current working directory, so before running the subcommand, make sure you are in the directory of the Puppetfile you want to use.
