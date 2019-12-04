---
layout: default
title: "Configuring r10k"
canonical: "/pe/latest/r10k_config.html"
description: "Configuring the r10k code management tool with Puppet Enterprise installation."
---

[repo]: ./cmgmt_control_repo.html
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

This page assumes that you have already set up a [control repo][control_repo] and a [Puppetfile][puppetfile].

To configure r10k:

* During a clean PE installation, [provide parameters](#configure-during-pe-installation) to `pe.conf`, **or**
* After installation or upgrade, [set parameters](#configure-after-pe-installation) in the master profile class in the PE console.

If you are upgrading from previous version of r10k, see [Upgrading from previous versions of r10k](#upgrading-from-previous-versions-of-r10k), below.

> Warning: Do not edit r10k's configuration manually. Puppet manages this configuration file automatically and will undo any manual changes you make.

## Configure during PE installation

During a fresh installation of Puppet Enterprise, you can provide parameters in `pe.conf` that automatically configure r10k. Note that these can only be used with [text-mode installation](./install_text_mode.html), not with the web-based installation.

You must add the following two parameters to `pe.conf` **before** installation:

#### puppet_enterprise::profile::master::r10k_remote

Accepts a string that is a valid SSH URL for your Git remote.

~~~
"puppet_enterprise::profile::master::r10k_remote": "git@<YOUR.GIT.SERVER.COM>:puppet/control.git"
~~~

#### puppet_enterprise::profile::master::r10k_private_key

The local filesystem path on the Puppet master where the SSH private key can be found and used by r10k.

Accepts a string, such as:

~~~
"puppet_enterprise::profile::master::r10k_private_key": "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
~~~

With the two answers above, the installer configures r10k for you. You can change the values for the remote and the private key as needed in the master profile [settings](#settings) in the PE console.

## Configure after PE installation

After a installing or upgrading PE, you can configure or adjust r10k settings in the master profile class in the PE console. The two parameters must be set for r10k to work. All other r10k settings use default values.

### Settings

#### `r10k_remote`

Accepts a string that is a valid URL for your Git remote, for example: `"git@yourgitserver.com:puppet/control.git"`.

#### `r10k_private_key`

The local filesystem path on the Puppet master where the SSH private key can be found and used by r10k. Accepts a string, such as `"/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"`.

### Upgrading from previous versions of r10k

If you used r10k prior to PE 2015.3, you might have configured r10k in the PE console using the `pe_r10k` class. If so, you aren't required to change that.

However, we suggest configuring r10k in the master profile class, and then customizing your configuration as needed in Hiera. This simplifies configuration, and makes it easier if you want to move to Code Manager in the future. 

To switch, remove the `pe_r10k` class in the PE console, and then configure as described [above](#configure-after-pe-installation). You can further customize your configuration [in Hiera][r10k_custom] as needed.

> Note: If you were using earlier versions of r10k with the zack-r10k module, switch to the master profile configuration as above.

## Next steps

If you need to customize your [r10k configuration][r10k_custom], you can do so via Hiera.

After r10k is configured, you can [run r10k][run]. PE does not automatically run r10k at the end of installation.
