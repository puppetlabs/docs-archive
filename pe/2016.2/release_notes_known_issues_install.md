---
layout: default
title: "Installation and upgrade known issues"
canonical: "/pe/latest/release_notes_known_issues_install.html"
---

This page lists known issues for installations and upgrades in Puppet Enterprise.

## **New:** Duplicate key in pe-classifier database after upgrade

In some cases, when upgrading to 2016.2.0, you may encounter an error during the database migration portion of the upgrade. This is due a duplicate key in the pe-classifier database. For a workaround, refer to the [PE database and PostrgreSQL known issues page](./release_notes_known_issues_puppetdb.html#new-duplicate-key-in-pe-classifier-database-after-upgrade). <!--PE-16812-->

## **New:** Upgrade fails if a `PE Infrastructure` node group name contains unexpected capitalization

When upgrading to 2016.2, installation fails if you changed the default capitalization for any `PE Infrastructure` node group names, because the installer is case insensitive. For example, if the installer encounters a `PE Mcollective` (lowercase "c") node group when it expects a `PE MCollective` (uppercase "c") node group, it returns an error about non-unique group names. <!--PE-18390-->

## **New:** Directory service setting overwritten on upgrades from 2015.3.x, 2016.1.x

When upgrading from the 2015.3 series or the 2016.1 series, the `disable_ldap_matching_rule_in_chain` directory service setting reverts to its default (`false`) setting. <!--PE-16354-->

## **New:** Removed `manage_kernel_shmmax` parameter

The `puppet_enterprise::profile::database::manage_kernel_shmmax` parameter is no longer used and has been removed. If you are upgrading and have this parameter set in your **PE PuppetDB** node group, you will receive an error message on your database node when you run the Puppet agent after the upgrade. You can safely remove this parameter from the classifier. <!--PE-8890-->

## Installer can fail due to SSL errors with AmazonAWS

In some cases when attempting to install PE, some master platforms have received SSL errors when attempting to connect to s3.amazonaws.com, and thus have been unable retrieve puppet-agent packages needed for installation. In most cases, you should be able to properly install after updating the CA cert bundle on the master platform. To update the bundle, run the following commands:

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

If you're upgrading, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading.

## Incorrect umask value can cause upgrade/installation to fail

To prevent potential failures, you should set an umask value of `0022` on your Puppet master.

## Before upgrading, correct invalid entries in `autosign.conf`

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

PostgreSQL does not support alt names when set to `verify_full`. If you are upgrading or installing in text-mode, make sure `puppet_enterprise::database_host` is set as the Puppet agent certname for the database node and not set as an alt name.

## Expired GPG Key causes node installation to fail on Debian and Ubuntu nodes

The GPG key bundled with versions prior to 2016.4.0 expired on 5 January 2017 causing installation to fail on Debian and Ubuntu nodes when PE packages are being added to the system. A workaround and working GPG key are available from our [knowledge base](https://support.puppet.com/hc/en-us/articles/222646307).