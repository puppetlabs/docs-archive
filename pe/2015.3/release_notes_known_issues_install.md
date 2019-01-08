---
layout: default
title: "Installation and Upgrade Known Issues"
canonical: "/pe/latest/release_notes_known_issues_install.html"
---

As we discover them, this page will be updated with known issues related to installations and upgrades of Puppet Enterprise 2015.3. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).

## **New:** Directory service setting overwritten on upgrades from 2015.3.x

When upgrading from earlier versions in the 2015.3 series, the `disable_ldap_matching_rule_in_chain` directory service setting reverts to its default (`false`) setting. Follow the [instructions in the RBAC API docs](./rbac_dsref.html#put-ds) to change this setting to `true`. <!--PE-16354-->

## **New:** Removed `manage_kernel_shmmax` parameter

The `puppet_enterprise::profile::database::manage_kernel_shmmax` parameter is no longer used and has been removed. If you are upgrading and have this parameter set in your **PE PuppetDB** node group, you will receive an error message on your database node when you run the Puppet agent after the upgrade. You can safely remove this parameter from the classifier. <!--PE-8890-->

## On upgrades from 3.8.x `fileserver.conf` customizations not preserved

If are upgrading from PE 3.8.x and have any customizations in `/etc/puppetlabs/puppet/fileserver.conf`, note that the customizations wil not be preserved. You will need to re-add your customizations after you complete your upgrade. <!--PE-13572-->

## When upgrading AIX agents, services may be stopped

When upgrading AIX agents, Puppet services may not properly start. You can run Puppet on these agents to restart the services.

<!--PE-13450-->

## PXP agent stopped after upgrading Solaris agents

After upgrading a Solaris agent, the PXP agent service may not properly restart. To restart the Puppet agent, run Puppet again or wait until the next timed run.
<!--PE-13918-->

## Installing and Upgrading to PE 2015.3 Requires Additional Steps to Configure the pe-orchestration Database

If you are upgrading a split install to PE 2015.3 please see [Upgrading to Puppet Enterprise 2015.3 Requires Additional Steps to Configure the pe-orchestration-database](./install_upgrading.html#upgrading-to-puppet-enterprise-20153-requires-additional-steps-to-configure-the-pe-orchestration-database) for information about additional steps you will perform during the upgrade.

If you are installing a split install using an answer file, you can supply your own username, database name, and password using the following:

- `q_orchestrator_database_name=` (`pe-orchestrator` is the default)
- `q_orchestrator_database_password=<Your Password>`
- `q_orchestrator_database_user=` (`pe-orchestrator` is the default)

## Do Not Use 'n' for File Sync Question in Answer File To Disable File Sync On Install

If you're using an answer file to install PE 2015.3 and want to disable file sync, do not use `q_puppetmaster_file_sync_service_enabled=n` in the answer file, as doing so creates an issue in which the PE classification will fail and the node groups will not be properly populated.

To workaround this issue, use `q_puppetmaster_file_sync_service_enabled=y`. After the installation completes, use the PE console to edit the `puppet_enterprise::profile::master` class and set the `file_sync_enabled` parameter to `false`.

## Installer Can Fail Due To SSL Errors with AmazonAWS

In some cases when attempting to install PE 2015.3, some master platforms have received SSL errors when attempting to connect to s3.amazonaws.com, and thus have been unable retrieve puppet-agent packages needed for installation. In most cases, you should be able to properly install after updating the CA cert bundle on the master platform. To update the bundle, run the following commands:

~~~
rm /etc/ssl/certs/ca-bundle.crt
yum reinstall ca-certificates
~~~

After updating the CA bundle, run the PE installer again.

## PE 3.8.x Agents Will Not Have MCollective and SymLinks Managed

More information is available in the [upgrade notes](./install_upgrading_notes.html#pe-38x-agents-will-not-have-mcollective-and-symlinks-managed)

## When Upgrading EL 7 or SLES 12 Agents, Do Not Use The `mco runonce` Command

Due to process handling on agent platforms using systemd (EL-7, SLES12), you cannot use `mco runonce` when upgrading Puppet on those agents. The systemd service kills all processes in the MCollective daemon's process group when the MCollective package is removed from the system during the upgrade process, and since MCollective started the Puppet run, Puppet is part of that process group. When this happens, the Puppet run is interrupted by the system, leaving the node in a half configured state with both 3.8 and 2015.3 agent packages installed.

## Incorrect Credentials for Console Databases will Cause Split Upgrade to Fail

If, during a split upgrade, you supply incorrect database credentials (specifically, incorrect database names, user names, or passwords for the databases associated with the PE console), the upgrade process will fail with a confusing error message. In most cases, ensure you have the correct database credentials and rerun the upgrader. The credentials can be found on the PuppetDB node at `/etc/puppetlabs/installer/database_info.install`.

## Web-based Installer Fails to Acknowledge Failed Installs due to Low RAM

When a PE installation fails because a system is not provisioned with adequate RAM, the web-based installer stops responding when verifying that PE is functioning on the server, but the installation appears to have succeeded, as the **Start using Puppet Enterprise** button is available. Note that in such cases, the command line shows an "out of memory: Kill process" error.

We recommend provisioning the system with adequate RAM and re-running the installation. Refer to the [hardware requirements](./install_system_requirements.html#hardware-requirements).

## Hard Tabs For Indentation In Hiera YAML Files Cause Errors After Upgrading

If you're upgrading to the PE 2015.3 series, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading.

## Customized PE Initialization Script Defaults Are Not Replaced on Upgrade

If you customized the `pe-console-services`, `pe-puppetserver`, or `pe-puppetdb` initialization scripts in `/etc/sysconfigs` (on RHEL-based systems) or `/etc/defaults` (on Debian-based systems) the customizations will be lost when you upgrade. This is mainly due to major path changes between versions. However, please note that the customized scripts will be saved in the same location with a `.bak` extension.

## Changes to `auth.conf` When Upgrading to Puppet Enterprise 2015.3

There have been several changes to `auth.conf` leading up to PE 2015.3. If you have a modified `auth.conf` file, you will be prompted by the upgrader to review it and make changes before continuing with your upgrade. For details, please review [Changes to `auth.conf` When Upgrading to Puppet Enterprise 2015.3](./install_upgrading_notes.html#changes-to-authconf-when-upgrading-to-puppet-enterprise20153).

## Incorrect Umask Value Can Cause Upgrade/Installation to Fail

To prevent potential failures, you should set an umask value of 0022 on your Puppet master.

## Compile Masters Installed Before 3.7.2 Cannot Be Upgraded

If you've installed compile masters in a version of PE before 3.7.2, you cannot upgrade these Puppet masters. To re-install and enable compile masters in 2015.3, refer to the [Additional Puppet Master Installation documentation](./install_multimaster.html).

## stdlib No Longer Installed with Puppet Enterprise

If necessary, you can install the stdlib module after installing/upgrading by running `puppet module install puppetlabs-stdlib`.

## Before Upgrading to PE 2015.3, Correct Invalid Entries in `autosign.conf`

Any entries in `/etc/puppetlabs/puppet/autosign.conf` that don't conform to the [autosign requirements](/puppet/4.2/reference/ssl_autosign.html#the-autosignconf-file) will cause the upgrade to fail to configure the PE console. Please correct any invalid entries before upgrading.

## Install Agents With Different OS When Puppet Master is Behind A Proxy

If your Puppet master uses a proxy server to access the internet, you may not be able to download the `pe_repo` packages for the agent. In the case that you're using a proxy, follow this workaround:

**Tip**: The following steps should be performed on your Puppet master (and, if you have a large environment installation, on all your compile masters as well).

1. From your Puppet master, navigate to `/etc/sysconfig/`, and create a file called `puppet`.
2. In `puppet` add the following lines:

   ~~~
   export http_proxy <YOUR_PROXY_SERVER>
   export https_proxy <YOUR_PROXY_SERVER>
   ~~~

3. Save and exit the file.
4. Restart the `puppet` service with the following commands:

   ~~~
   puppet resource service puppet ensure=stopped
   puppet resource service puppet ensure=running
   ~~~


## `q_database_host` Cannot be an Alt Name For Upgrades or Installs of 2015.3

PostgreSQL does not support alt names when set to `verify_full`. If you are upgrading to or installing 2015.3 with an answer file, make sure `q_database_host` is set as the Puppet agent certname for the database node and not set as an alt name.

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