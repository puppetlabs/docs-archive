---
layout: default
title: "Uninstalling Puppet Enterprise"
canonical: "/pe/latest/install_uninstalling.html"
---

Puppet Enterprise includes an script for uninstalling PE.

## Uninstall Puppet Enterprise

You can uninstall PE from the component infrastructure nodes or from agent nodes.

### Uninstall Puppet Enterprise from component nodes

The `puppet-enterprise-uninstaller` script is installed on the Puppet master, and in a split install, it's also installed on the PuppetDB, and the PE console nodes. In order to uninstall PE, you must run the uninstaller on each component node.

By default, the uninstaller removes the Puppet Enterprise software, users, logs, cron jobs, and caches, but it leaves your modules, manifests, certificates, databases, and configuration files in place, as well as the home directories of any users it removes.

The uninstaller must be run with `root` privileges.

Refer to [Uninstaller options](#uninstaller-options) for a list of flags you can use to change the uninstaller's behavior.

1. From the command line of the Puppet Enterprise component node, navigate to the installer directory and run the following command:

   ~~~
   $ sudo ./puppet-enterprise-uninstaller
   ~~~

   >**Tip:** If you don't have access to the installer directory, you can run `/opt/puppetlabs/bin/puppet-enterprise-uninstaller`.

2. Follow the uninstaller prompts.

3. (**Optional if you do not uninstall the Puppet master**) If you plan to reinstall PE on a component node at a later date, remove the agent certificate for that component from the Puppet master. On the Puppet master, run the following command:

   ~~~
   puppet cert clean <PE COMPONENT CERT NAME>
   ~~~

### Uninstaller options

You can use the following command-line flags to change the uninstaller's behavior.

* `-p` --- Purge additional files. With this flag, the uninstaller will also remove all configuration files, modules, manifests, certificates, and the home directories of any users created by the PE installer. This will also remove the Puppet public GPG key used for package verification.
* `-d` --- Also remove any databases created during installation.
* `-h` --- Display a help message.
* `-n` --- Run in `noop` mode; show commands that would have been run during uninstallation without actually running them.
* `-y` --- Don't ask to confirm uninstallation, assuming an answer of yes.

Thus, to remove every trace of PE from a system, you would run:

    $ sudo ./puppet-enterprise-uninstaller -d -p

### Uninstall Puppet Enterprise from *nix agent nodes

In PE, *nix agents do not include the uninstaller as part of the agent package. There are additional steps you'll need to take to uninstall PE from agent nodes.

>**Note:** For instructions on uninstalling Puppet on Windows agents, refer to the  [uninstalling section](./install_windows.html#uninstalling) of the Windows agent installation instructions.


1. **On your Puppet master**, navigate to `/opt/puppetlabs/bin/` and copy `puppet-enterprise-uninstaller` to the agent node you're uninstalling PE from.
2. **On the agent node**, run sudo `./puppet-enterprise-uninstaller`, and follow the prompts.
3. (**Optional**) If you plan to reinstall PE on that node at a later date, remove the agent certificate for the agent from the Puppet master. On the Puppet master, run the following command:

   ~~~
   puppet cert clean <AGENT CERT NAME>
   ~~~

### Uninstall Puppet Enterprise from Mac OS X agent nodes

When you use the OS X uninstaller, you will completely remove all aspects of the PE agent from that node.

1. In the original OS X PE agent package you downloaded, run the uninstaller `.pkg`.
2. Follow the instructions in the uninstall dialog.
3. (**Optional**) If you plan to reinstall PE on that node at a later date, remove the agent certificate for the agent from the Puppet master. On the Puppet master, run the following command:

   ~~~
   puppet cert clean <AGENT CERT NAME>
   ~~~


* * *