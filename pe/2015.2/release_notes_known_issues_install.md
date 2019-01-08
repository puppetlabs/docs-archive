---
title: "PE 2015.2 » Release Notes >> Known Issues"
layout: default
subtitle: "Installation and Upgrade Known Issues"
canonical: "/pe/latest/release_notes_known_issues_install.html"
---

As we discover them, this page will be updated with known issues related to installations and upgrades of Puppet Enterprise 2015.2 releases. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).


## **New:** Removed `manage_kernel_shmmax` parameter

The `puppet_enterprise::profile::database::manage_kernel_shmmax` parameter is no longer used and has been removed. If you are upgrading and have this parameter set in your **PE PuppetDB** node group, you will receive an error message on your database node when you run the Puppet agent after the upgrade. You can safely remove this parameter from the classifier. <!--PE-8890-->

## On upgrades from 3.8.x `fileserver.conf` customizations not preserved

If are upgrading from PE 3.8.x and have any customizations in `/etc/puppetlabs/puppet/fileserver.conf`, note that the customizations wil not be preserved. You will need to re-add your customizations after you complete your upgrade. <!--PE-13572-->

## Upgrading Puppet agents can leave two MCollective processes running

If you upgrade RHEL 4, 5, or 6 Puppet agents using the PE agent upgrade script, you may encounter a situation in which two MCollective processes are running on the agent.

To workaround this issue, you can terminate one of the processes—the process **not** identified by `/var/run/puppetlabs/mcollective.pid`. This error is a limitation of the upgrade script and will not reoccur after terminating the extra process.

This issue does not affect the Puppet master agent, the PE console agent, the PuppetDB agent, or any compile master agents.

<!--PE-13973-->

## MCollective class filtering broken on upgrades from 3.8.x

If you upgrade from PE 3.8.x, the Puppet agent's `classfile` setting in `puppet.conf` is not reset to the new default location for new (AIO) Puppet agents.

This only affects nodes in the PE core infrastructure:

* The PE Master node (the master of masters in a LEI install; compile masters are not affected)
* The PE console node
* The PuppetDB node

To workaround this issue after upgrading, run the following commands:

~~~
puppet config set --section agent classfile /opt/puppetlabs/puppet/cache/state/classes.txt
puppet agent -t
~~~

<!--PE-13972-->

## PE 3.8.x Agents Will Not Have MCollective and SymLinks Managed

More information is available in the [upgrade notes](./install_upgrading_notes.html#pe-38x-agents-will-not-have-mcollective-and-symlinks-managed)

## Puppet Agent Service Does Not Properly Stop When Upgrading Compile Masters on Ubuntu Platforms

If you are upgrading Ubuntu compile masters, you may encounter a situation in which the Puppet agent service is not properly stopped when the new agent is installed (which is part of the upgrade process). When it's time for the agent service to start and run Puppet, an error is raised that indicates a run is already in progress: `Notice: Run of Puppet configuration client already in progress; skipping (/var/opt/lib/pe-puppet/state/agent_catalog_run.lock exists)`.

This is due to the fact that the agent service didn't stop. This causes the upgrade to fail.

To workaround this issue:

1. On your Master of Master (MoM), ensure the permissions for `/opt/puppetlabs/server/data/packages/public/current/pe-code-migration.rb` are set to 644.

2. On your Ubuntu compile master, run the following commands:

   ~~~
   export HIERA_YAML_BACKUP=/etc/puppetlabs/puppet/hiera.yaml-<TIMESTAMP>.bak
   curl -k https://<MASTER OF MASTERS HOSTNAME>:8140/packages/current/pe-code-migration.rb | /opt/puppetlabs/puppet/bin/ruby
   ~~~

3. On your Ubuntu compile master, kick off a Puppet run with `puppet agent -t`.

4. On your Ubuntu compile master, restart to the `puppet` service with `service puppet restart`.

## When Upgrading EL 7 or SLES 12 Agents, Do Not Use the `mco runonce` Command

Due to process handling on agent platforms using systemd (EL-7, SLES12), you cannot use `mco runonce` when upgrading Puppet on those agents. The systemd service kills all processes in the MCollective daemon's process group when the MCollective package is removed from the system during the upgrade process, and since MCollective started the Puppet run, Puppet is part of that process group. When this happens, the Puppet run is interrupted by the system, leaving the node in a half configured state with both 3.8 and 2015.2 agent packages installed.

## Incorrect Credentials for Console Databases Will Cause Split Upgrade to Fail

If, during a split upgrade, you supply incorrect database credentials (specifically, incorrect database names, user names, or passwords for the databases associated with the PE console), the upgrade process will fail with a confusing error message. In most cases, ensure you have the correct database credentials and rerun the upgrader. The credentials can be found on the PuppetDB node at `/etc/puppetlabs/installer/database_info.install`.

## Web-Based Installer Fails to Acknowledge Failed Installs Due to Low RAM

When a PE installation fails because a system is not provisioned with adequate RAM, the web-based installer stops responding when verifying that PE is functioning on the server, but the installation appears to have succeeded, as the **Start using Puppet Enterprise** button is available. Note that in such cases, the command line shows an "out of memory: Kill process" error.

We recommend provisioning the system with adequate RAM and re-running the installation. Refer to the [hardware requirements](./install_system_requirements.html#hardware-requirements).

## Hard Tabs For Indentation In Hiera YAML Files Cause Errors After Upgrading

If you're upgrading to the PE 2015.2.x series, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading.

## Customized PE Initialization Script Defaults Are Not Replaced on Upgrade

If you customized the `pe-console-services`, `pe-puppetserver`, or `pe-puppetdb` initialization scripts in `/etc/sysconfigs` (on RHEL-based systems) or `/etc/defaults` (on Debian-based systems) the customizations will be lost when you upgrade. This is mainly due to major path changes between versions. However, please note that the customized scripts will be saved in the same location with a `.bak` extension.

## Review Modified `auth.conf` Files before Upgrading to 2015.2.x

There have been several changes to `auth.conf` leading up to PE 2015.2. If you have a modified `auth.conf` file, you will be prompted by the upgrader to review it and make changes before continuing with your upgrade. For details, please review [Upgrading to Puppet Enterprise 2015.2 with a Modified `auth.conf` File](./install_upgrading_notes.html#upgrading-to-38-with-a-modified-authconf-file).

## Incorrect Umask Value Can Cause Upgrade/Installation to Fail

To prevent potential failures, you should set an umask value of 0022 on your Puppet master.

## stdlib No Longer Installed with Puppet Enterprise

If necessary, you can install the stdlib module after installing/upgrading by running `puppet module install puppetlabs-stdlib`.

## Install Agents With Different OS When Puppet Master is Behind A Proxy

If your Puppet master uses a proxy server to access the internet, you may not be able to download the `pe_repo` packages for the agent. In the case that you're using a proxy, follow this workaround:

**Tip**: The following steps should be performed on your Puppet master (and, if you have a large environment installation, on all your compile masters as well).

1. From your Puppet master, navigate to `/etc/sysconfig/`, and create a file called `pe-puppet`.
2. In `pe-puppet` add the following lines:

   ~~~
   export http_proxy <YOUR_PROXY_SERVER>
   export https_proxy <YOUR_PROXY_SERVER>
   ~~~

3. Save and exit the file.
4. Restart the `puppet` service with the following commands:

   ~~~
   puppet resource service pe-puppet ensure=stopped
   puppet resource service pe-puppet ensure=running
   ~~~

## Notes about Symlinks and Installation/Upgrade

The answer file no longer gives the option of whether to install symlinks.

Review [Puppet Enterprise Binaries and Symlinks](./install_basic.html#puppet-enterprise-binaries-and-symlinks) for more information about the binaries and symlinks installed during normal installations and upgrades.

## Answer File Required for Some SMTP Servers

Any SMTP server that requires authentication, TLS, or runs over any port other than 25 needs to be explicitly added to an answers file. See the [advanced configuration page](./console_config.html#allowing-anonymous-console-access) for details.

## Installing Agents in a PE Infrastructure with No Internet Access

Refer to [Installing Agents in a Puppet Enterprise Infrastructure without Internet Access](./install_agents.html#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access).

## Agent-Only Installs Using An Answer File Are Not Supported

You cannot use an answer file to install a Puppet agent only. See [Installing Puppet Enterprise Agents](./install_agents.html) for agent installation instructions. (For Windows, see [Installing Windows Agents](./install_windows.html) and for Mac OS X, see [Installing Mac OS X Agents](.install_osx.html).

## Expired GPG Key causes node installation to fail on Debian and Ubuntu nodes

The GPG key bundled with versions prior to 2016.4.0 expired on 5 January 2017 causing installation to fail on Debian and Ubuntu nodes when PE packages are being added to the system. A workaround and working GPG key are available from our [knowledge base](https://support.puppet.com/hc/en-us/articles/222646307).