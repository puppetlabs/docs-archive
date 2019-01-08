---
layout: default
title: "PE 2015.2 » Code Management with r10k » Configuring r10k with the Answers File"
subtitle: "Configuring r10k with the Answers File"
canonical: "/pe/latest/r10k_config_answers.html"
description: "Configuring r10k code management with Puppet Enterprise installation."
---

[environ_dir]: /puppet/latest/reference/environments_configuring.html
[install_prep]: ./r10k_install_prep.html
[config_console]: ./r10k_config_console.html
[puppetfile]: ./r10k_puppetfile.html
[run]: ./r10k_run.html
[r10kindex]: ./r10k.md
[puppetfile]: ./r10k_puppetfile.html
[answer_file]: ./install_automated.html

If you are starting with a fresh installation of Puppet Enterprise 2015.2 or later, you can provide answers that allow Puppet Enterprise to automatically configure r10k. Note that these can only be used with command-line installation using an answer file; the web-based installer does not currently support configuration of r10k.

If you already have an existing PE installation, or you used the web-based installer, you'll instead configure r10k [in the PE console][config_console].

## Installation

To enable Puppet Enterprise to automatically configure r10k, provide your [Git remote and the private key][install_prep] to the [answers file][answer_file] during your PE installation.

#### `q_puppetmaster_r10k_remote`

Accepts a string that is a valid URL for your Git remote.

`q_puppetmaster_r10k_remote='git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`

#### `q_puppetmaster_r10k_private_key`

The local filesystem path on the Puppet master where the SSH private key can be found and used by r10k.

Accepts a string, such as:

`q_puppetmaster_r10k_private_key=/root/.ssh/id_dsa`

## Settings

With the two answers above, the installer configures r10k for you. You can change the values for the remote and the private key as needed in the master profile in the PE console. All other r10k settings use default values.

Note that the following settings cannot be changed in the PE console if r10k is configured through the answer file:

* `basedir`: This setting defaults to `etc/puppetlabs/code/environments`. This setting **must** match `environmentpath` in your puppet.conf file.
* `provider`: Only the `rugged` provider is supported in this configuration method. The `rugged provider` uses the Git library built into r10k. 

If you need shellgit support, multiple sources, or other more complex r10k configurations, see [Configuring r10k in the PE Console][config_console].

## Next Steps

If you don't yet have a Puppetfile, you can [set one up][puppetfile] now. If you have a Puppetfile in your repo already, you can now [run r10k][run]. PE does not automatically run r10k at the end of installation.