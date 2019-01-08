---
layout: default
title: "Configuring Code Manager"
canonical: "/pe/latest/code_mgr_config.html"
description: "How to configure Code Manager, a system for managing and deploying Puppet code."
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
[upgrade]: ./code_mgr_upgrade.html
[filesync]: ./cmgmt_filesync.html

If you're configuring Code Manager during or after a clean install of PE:

* During a fresh installation, provide answers to the answer file, **or**
* After installation, set parameters in the master profile class in the PE console.

If you are setting up Code Manager after an upgrade from a previous installation of PE, see [Setting up Code Manager with a PE upgrade][upgrade].

> Warning: Do not edit Code Manager’s configuration manually. Puppet manages this configuration file automatically and undoes any manual changes you make.

## Configure During PE installation

The easiest way to configure Code Manager is to add answers to the answer file during installation, which automatically configures both Code Manager and r10k. These answers can only be used with command-line installation using an answer file, not web-based installation.

Note that you must add these answers to the answer file **before** installation.

Provide the following three answers in the answers file:

### `q_puppetmaster_r10k_remote`

The location of the Git control repository. Accepts a string that is a valid URL for your Git remote. For example:

`q_puppetmaster_r10k_remote='git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`

### `q_puppetmaster_r10k_private_key`

The path to the file that contains the SSH private key used to access all Git repositories. This file should be located on the Puppet master and must be owned by the `pe-puppet` user.

Accepts a string, such as:

`q_puppetmaster_r10k_private_key='/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'`

> **After** your PE installation is complete, place the private key you created when you [set up your Git repo](./cmgmt_control_repo.html#set-up-your-git-repo) in this location. This must be a location that the `pe-puppet` user can access; we recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. The file itself should have the correct permissions for the `pe-puppet` user to use that key.

### `q_puppetmaster_code_manager_auto_configure`

This configures Code Manager using the master profile in the PE console, along with the Git remote to use for storing code, and the private key for accessing your Git repos.

Accepts 'y' or 'n'. Set to 'y' to auto-configure Code Manager. If you set it to 'n', Code Manager is disabled.

After providing these answers and finishing your PE install, you can adjust parameters as needed in the master profile class.

## Configure After PE Installation

Alternatively, if you didn't provide answers to the command line installation, you can configure Code Manager or adjust an existing Code Manager configuration by setting the following values in the `puppet_enterprise::profile::master` profile class. Unless otherwise stated, these parameters are required for Code Manager to work.

* `code_manager_auto_configure`: Set to true to auto-configure Code Manager. Setting this parameter to false disables Code Manager.
* `file_sync_enabled`: Set to true, so that file sync is enabled. This parameter defaults to true on fresh installations, and to false on upgrades. If you are upgrading a previous installation of PE, see Code Manager's [upgrade page][upgrade] for how to enable this properly.
  Setting this parameter to false disables file sync and causes Code Manager to deploy directly to the live code directory (`/etc/puppetlabs/code`). This configuration is not recommended.
* `r10k_remote`: The location of the Git control repository. Accepts a string that is a valid URL for your Git remote. For example: `'git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`.
* `r10k_private_key`: This is the path to the private key that permits the `pe-puppet` user to access all Git repositories. This file must be owned by the `pe-puppet` user. Accepts a string, such as `'/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'`.
* `r10k_proxy`: Optional. A proxy setting r10k uses when accessing the Forge. If empty, no proxy settings are used.

After adjusting these parameters, run Puppet on the master to apply the changes.

## Next Steps

After you've got Code Manager configured, you're ready to set up a trigger for Code Manager with a [webhook][code_mgr_webhook] or a [custom script][scripts].

You can also [customize your Code Manager configuration][code_mgr_custom], if necessary.

