---
layout: default
title: "Text-mode installation overview"
canonical: "/pe/latest/install_text_mode.html"
---


## How text-mode installation works

When you run the Puppet Enterprise installer in text mode, you pass a configuration file (`pe.conf`), which contains values for the parameters needed to install PE, to the installer.

Text-mode installation requires you to run the installer with a `pe.conf` file on each node on which you are installing a PE component. In other words, for a monolithic installation, you run the installer with `pe.conf` on one node, but for a split installation, you run the installer with `pe.conf` on three nodes.

* [Monolithic text-mode installation instructions](./install_text_mode_mono.html)
* [Split text-mode installation instructions](./install_text_mode_split.html)

### The `pe.conf` file

A `pe.conf` file is a [HOCON](./config_hocon.html) formatted file that declares parameters and values needed to install and configure PE. For example, for a monolithic installation, you only need two key/value pairs:

~~~
"console_admin_password": "puppet"
"puppet_enterprise::puppet_master_host": "%{::trusted.certname}"
~~~

See the [`pe.conf` parameter reference](./install_pe_conf_param.html) for more details about setting parameters in `pe.conf` and for a complete list of mandatory and optional parameters.

### Obtaining a `pe.conf` file

You have a few options for creating or obtaining a `pe.conf` file to use during installations.

* Use the example `pe.conf` file provided in the `conf.d` directory in the PE installer tarball.

   >**Tip:** In most cases, for a monolithic installation, you can use the example `pe.conf` file without making any changes.

* When prompted by the installer, select the text-mode installation option. This opens your default editor with the example `pe.conf` file, which you can edit. Installation will proceed using that `pe.conf` after you quit the editor.
* Use the web-based installer to create a `pe.conf` file. After you run the web-based installer, you can find the file at `/etc/puppetlabs/enterprise/conf.d`. You can also download the file by following the link provided on the confirmation page of the web-based installer.

For a complete list of mandatory and optional parameters, see the [`pe.conf` parameter reference](./install_pe_conf_param.html).

## Configuring PE settings with Hiera when installing

Any PE settings you [configure via Hiera](./config_intro.html#configure-settings-with-hiera) in the default `datadir` (`/etc/puppetlabs/code/environments/%{environment}/hieradata`) will be respected by `pe.conf` when you upgrade.

If you are using **a non-default location** for the Hiera `datadir` you can add those Hiera parameters or overrides to `pe.conf` when upgrading, but note that you must add them to your production Hiera when the installation completes, or they will be overwritten when Puppet runs.
