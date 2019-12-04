---
layout: default
title: "Configuring r10k"
canonical: "/pe/latest/r10k_config.html"
description: "Configuring the r10k code management tool with Puppet Enterprise installation."
---

[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[r10k_config]: ./r10k_config.html
[r10k_custom]: ./r10k_custom.html
[r10k_run]: ./r10k_run.html
[r10k_ref]: ./r10k_ref.html

[environ_dir]: {{puppet}}/environments_configuring.html
[run]: ./r10k_run.html


When performing a fresh text-mode installation of PE, you can configure r10k by adding parameters to the `pe.conf` file. In existing installations, configure r10k by adjusting parameters in the PE console. 

> **Warning:** Do not edit r10k's configuration manually. Puppet manages this configuration file automatically and will undo any manual changes you make.

## Configure r10k during PE installation

When installing Puppet Enterprise using the [text-mode installation](./install_text_mode.html) method, provide two parameters in `pe.conf` to automatically configure r10k. 

> **Note:** If using the [web-based installer](./install_pe_mono.html), complete PE installation and then configure r10k using the instructions in [Configure r10k after PE installation](#configure-after-pe-installation).  

Before you begin: Set up a [Git control repo][./cmgmt_control_repo.html] and a [Puppetfile][puppetfile].

1. Before beginning PE installation, add two parameters to `pe.conf`:

   1. Add `puppet_enterprise::profile::master::r10k_remote` to `pe.conf.

   This setting specifies the location of the control repository. It accepts a string that is a valid URL for your Git control repository. For example:

   ```
"puppet_enterprise::profile::master::r10k_remote": "git@<YOUR.GIT.SERVER.COM>:puppet/control.git"
   ```
   2. Add `puppet_enterprise::profile::master::r10k_private_key` to `pe.conf`.

   This setting specifies the path to the file that contains the SSH private key used to access your Git repositories. The `pe-puppet` user *must* be able to access this location. The setting accepts a string, such as:

   ```
"puppet_enterprise::profile::master::r10k_private_key": "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
   ```
  
  This SSH private key file, which you created when you [set up your control repository](./cmgmt_control_repo.html#set-up-your-git-repo), must be located on the Puppet master and owned by the `pe-puppet` user. 

2. Complete PE installation. The installer configures r10k for you. You can change the values for the remote and the private key as needed in the master profile [settings](#settings) in the PE console.

3. After installation is complete, place the SSH private key you created when you [set up your control repository](./cmgmt_control_repo.html#set-up-your-git-repo) in the `r10k_private_key` location.

   You can now customize your [r10k configuration][r10k_custom], if needed.

4. After r10k is configured, you can [run r10k][run]. PE does not automatically run r10k at the end of installation.

## Configure r10k after PE installation

To configure r10k after installing PE or in an existing PE installation, set parameters in the PE console. You can also adjust r10k's settings here.

Before you begin: Set up a [Git control repo][./cmgmt_control_repo.html] and a [Puppetfile][puppetfile].

1. In the console, set the following parameters in the `puppet_enterprise::profile::master` class in the **PE Master** node group:

  * `r10k_remote`: This is the location of your control repository. Enter a string that is a valid URL for your Git control repository, such as `"git@<YOUR.GIT.SERVER.COM>:puppet/control.git"`.
  
  * `r10k_private_key`: This is the path to the private key that permits the `pe-puppet` user to access your Git repositories. This file must be located on the Puppet master, owned by the `pe-puppet` user, and in a directory that the `pe-puppet` user has permission to view. We recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. Enter a string, such as `"/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"`.

2. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on all of your masters.

   You can now customize your [r10k configuration][r10k_custom], if needed.

3. After r10k is configured, you can [run r10k][run]. PE does not automatically run r10k at the end of installation.

## Upgrading from previous versions of r10k

If you used r10k prior to PE version 2015.3, you might have configured r10k in the PE console using the `pe_r10k` class. If so, no changes are required.

However, we suggest configuring r10k in the master profile class, and then customizing your configuration as needed in Hiera. This simplifies configuration, and makes it easier if you want to move to Code Manager in the future. 

To switch to master profile class configuration, remove the `pe_r10k` class in the PE console, and then configure r10k as described in [Configure r10k after PE installation](#configure-after-pe-installation). You can further customize your configuration [in Hiera][r10k_custom] as needed.

> **Note:** If you were using earlier versions of r10k with the zack-r10k module, switch to the master profile configuration as above.

