---
layout: default
title: "Installation and upgrade known issues"
canonical: "/pe/latest/release_notes_known_issues_install.html"
---

This page lists known issues for installations and upgrades in Puppet Enterprise 2016.1.

## **New:** Directory service setting overwritten on upgrades from 2015.3.x

When upgrading from the 2015.3 series, the `disable_ldap_matching_rule_in_chain` directory service setting reverts to its default (`false`) setting. Follow the [instructions in the RBAC API docs](./rbac_dsref_v1.html#note-matching-rule-in-chain) to change this setting to `true`.
<!--PE-16354-->

## **New:** On upgrades from 3.8.x `fileserver.conf` customizations not preserved

If are upgrading from PE 3.8.x and have any customizations in `/etc/puppetlabs/puppet/fileserver.conf`, note that the customizations wil not be preserved. You will need to re-add your customizations after you complete your upgrade. <!--PE-13572-->

## **New:** Remove deprecated pe_repo classes after upgrade

Debian 6 and Ubuntu 15.04 and 15.10 have been removed as Puppet agent platforms. After upgrading, use the PE console to remove these repos from the Puppet master.

**To remove the deprecated repos:**

1. In the PE console, navigate to the PE Master node group and click the **Classes** tab.
2. Locate the pe_repo classes for the removed platforms, and click **Remove this class**.
3. Click the **Commit changes** button.
4. Run Puppet on the Puppet master.

<!--PE-15251-->

## PostreSQL `max_connections` set to 200 on upgrades

Due to an upgrade known issue in 2016.1.1, your PostgreSQL `max_connections` settings will be to set to 200. If you had a higher number of `max_connections` set, you can change the setting back when the upgrade is complete.

<!--PE-14916-->

## The pe-puppetserver service may not complete its restart when enforcing changes on master of masters

In some cases, if you make a configuration change to the master of masters (MoM), the Puppet agent run that enforces the change on the MoM may fail because the pe-puppetserver service has not completed its restart.

<!--PE-14569-->

## Installer can fail due to SSL errors with AmazonAWS

In some cases when attempting to install PE 2015.2, some master platforms have received SSL errors when attempting to connect to s3.amazonaws.com, and thus have been unable retrieve puppet-agent packages needed for installation. In most cases, you should be able to properly install after updating the CA cert bundle on the master platform. To update the bundle, run the following commands:

~~~
rm /etc/ssl/certs/ca-bundle.crt
yum reinstall ca-certificates
~~~

After updating the CA bundle, run the PE installer again.

## Incorrect credentials for console databases will cause split upgrade to fail

If, during a split upgrade, you supply incorrect database credentials (specifically, incorrect database names, user names, or passwords for the databases associated with the PE console), the upgrade process will fail with a confusing error message. In most cases, ensure you have the correct database credentials and rerun the upgrader. The credentials can be found on the PuppetDB node at `/etc/puppetlabs/installer/database_info.install`.

## Web-based installer fails to acknowledge failed installs due to low RAM

When a PE installation fails because a system is not provisioned with adequate RAM, the web-based installer stops responding when verifying that PE is functioning on the server, but the installation appears to have succeeded, as the **Start using Puppet Enterprise** button is available. Note that in such cases, the command line shows an "out of memory: Kill process" error.

We recommend provisioning the system with adequate RAM and re-running the installation. Refer to the [hardware requirements](./install_system_requirements.html#hardware-recommendations).

## Hard tabs for indentation in Hiera YAML files cause errors after upgrading

If you're upgrading to the PE 2015.2.x series, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading.

## Customized PE initialization script defaults are not replaced on upgrade

If you customized the `pe-console-services`, `pe-puppetserver`, or `pe-puppetdb` initialization scripts in `/etc/sysconfigs` (on RHEL-based systems) or `/etc/defaults` (on Debian-based systems) the customizations will be lost when you upgrade. This is mainly due to major path changes between versions. However, please note that the customized scripts will be saved in the same location with a `.bak` extension.

## Incorrect umask value can cause upgrade/installation to fail

To prevent potential failures, you should set an umask value of 0022 on your Puppet master.

## stdlib no longer installed with Puppet Enterprise

If necessary, you can install the stdlib module after installing/upgrading by running `puppet module install puppetlabs-stdlib`.

## Before upgrading to PE 2015.2, correct invalid entries in `autosign.conf`

Any entries in `/etc/puppetlabs/puppet/autosign.conf` that don't conform to the [autosign requirements]({{puppet}}/ssl_autosign.html#the-autosignconf-file) will cause the upgrade to fail to configure the PE console. Please correct any invalid entries before upgrading.

## Install agents with different OS when Puppet master is behind a proxy

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


## `q_database_host` cannot be an alt name for upgrades or installs

PostgreSQL does not support alt names when set to `verify_full`. If you are upgrading or installing with an answer file, make sure `q_database_host` is set as the Puppet agent certname for the database node and not set as an alt name.

## Expired GPG Key causes node installation to fail on Debian and Ubuntu nodes

The GPG key bundled with versions prior to 2016.4.0 expired on 5 January 2017 causing installation to fail on Debian and Ubuntu nodes when PE packages are being added to the system. A workaround and working GPG key are available from our [knowledge base](https://support.puppet.com/hc/en-us/articles/222646307).