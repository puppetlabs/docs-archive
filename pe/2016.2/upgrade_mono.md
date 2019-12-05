---
layout: default
title: "Upgrading Puppet Enterprise: Monolithic"
canonical: "/pe/latest/upgrade_mono.html"
---

This guide provides upgrade instructions for a monolithic installation. For instructions on upgrading a split installation, refer to [Upgrading Puppet Enterprise: Split](./upgrade_split.html).

## Before beginning: review 2016.2.0 upgrade information

We support the following upgrade paths:

<table>
  <tr>
    <th>Upgrading from</th>
    <th>We recommend upgrade to</th>
  </tr>
  <tr>
   <td>
     <ul>
      <li>2015.2.3</li>
      <li>2015.3.3</li>
      <li>2016.1.2</li>
      <li>2016.2.0</li>
     </ul>
    </td>
    <td>2016.2.1</td>
    </tr>
</table>

### On PE 3.8?

If you are on PE 3.8, you need to follow the [3.8 to 2016.2 migration documentation](./migrate_monolithic.html), as upgrades from 3.8 are not supported.

In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions.


### Upgrade overview

The upgrade process consists of the following high-level steps:

1. **For external PostgreSQL users:** Prepare your external PostgreSQL instance for upgrade.
2. Using legacy `auth.conf`? Prepare your `auth.conf` for upgrade.
3. Upgrade your monolithic installation.
4. Upgrade any compile masters.
5. Upgrade your Puppet agents.

## Upgrading an external PostgreSQL instance

These tasks should be done **BEFORE** starting the upgrade.

If you prefer to use an existing instance of PostgreSQL, that instance needs to be manually configured with the correct users and access, and it must be version 9.4.

If you are using an external PostgreSQL instance, please note the following:

1. If you have not already done so, you need to create databases for RBAC, activity service, and the node classifier before upgrading. **If you are upgrading from PE 2015.2.3,** you also need to create the orchestrator database.

2. **If you are upgrading from 2015.2.3 or 2015.3.3,** and have not already done so, you need to enable the citext extension on the RBAC database, and the `pg_trgm` and `pgcrypto` extensions on the PuppetDB database.

3. The [`pe.conf` parameter reference](./install_pe_conf_param.html#external-postgresql-parameters) contains required parameters you must add when performing an install or upgrade with an external PostgreSQL instance.

[External PostgreSQL prep notes](./install_pe_split.html#external-postgresql-prep-notes) provides more information about setting up these databases and extensions.

## Changes to `auth.conf` when upgrading

In previous versions of PE, the `auth.conf` file managed access to Puppet’s HTTPS API. This file was located at `/etc/puppetlabs/puppet/auth.conf` on the Puppet master.

We’ve introduced a new `auth.conf` (`/etc/puppetlabs/puppetserver/conf.d/auth.conf`) that makes use of Puppet Server’s trapper-keeper services.

**If you have not already done so in a previous upgrade,** we recommend that you address the changes, as some functionality (e.g., console services) may not be available after upgrade if the endpoints aren’t authorized.

When upgrading to PE 2016.2 you have two choices:

- You can continue to use your legacy auth.conf file, but you must update it to include the endpoints introduced in the both the 2015.2 and 2015.3 series. You must make the necessary changes to your existing auth.conf file and add `puppet_enterprise::master::use_legacy_auth_conf: true` to your `pe.conf` file before the upgrade.
- You can upgrade to 2016.2 and then modify the new auth.conf file to include any customizations from your legacy file. This is the default action if no parameter is added to your pe.conf file.

Refer to [Puppet Server Configuration Files: Migrating to the HOCON auth.conf Format]({{puppetserver}}/config_file_auth_migration.html) for instructions on migrating to the new format.

### Notable changes to auth.conf:

In 2016.1.1, two new endpoints were added to the new auth.conf:

- `/puppet/v3/environment_classes`
- `/puppet/v3/static_file_content`

These new endpoints are not compatible with legacy auth.conf files.

## Upgrading a monolithic installation

**Before you begin:** If your master is set to noop mode (`noop = true` in `puppet.conf` on the master), remove or comment out this flag prior to upgrade. The installer fails if you attempt to upgrade in noop mode.

1. If necessary, review [Upgrading an external PostgreSQL instance](#upgrading-an-external-postgresql-instance).
2. If you have used **a non-default location** for the Hiera `datadir`, or configured any settings via the PE console, you **must** add these settings to `pe.conf` during the following steps, or they will be overwritten when Puppet runs.
3. [Download](./install_basic.html#downloading-puppet-enterprise) and unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
4. From the installer directory, run the installer/upgrader. The steps will vary depending on the path you choose.

   * To use a `pe.conf` file that already exists, run the upgrade **with the -c flag** pointed at the `pe.conf` file. Your previous `pe.conf` will be backed up to `/etc/puppetlabs/enterprise/conf.d/timestamp.conf` and your new file will be moved to `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

     ~~~
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ~~~

   * To have the installer/upgrader open a copy of `pe.conf` for you to edit and use, run the upgrade **without the `-c` flag**:

     ~~~
     sudo ./puppet-enterprise-installer
     ~~~

    In either case, the **the following parameter is required:**

      - `"console_admin_password": "<PASSWORD>"`— Replace `<PASSWORD>` with your password used to log into the PE console.

        >**Tip**: On a monolithic upgrade, you can leave the default value of `"%{::trusted.certname}"`if the certname of your monolithic server matches its FQDN.

    You can also add [any additional text-mode installation options as needed](./install_pe_conf_param.html#additional-parameters-for-monolithic-and-split-installations). Additionally, if you have an external PostgreSQL node or you have customized the names of the databases in your installation please refer to the [external PostgreSQL parameters in the pe.conf reference](./install_pe_conf_param.html#external-postgresql-parameters).


5. Follow the installer prompts to continue.

## Rerunning the upgrade

The PE installer/upgrader is *idempotent*, meaning that you can run it as many times as needed without affecting the outcome. If you encounter any errors during upgrade, you can fix them and run the installer again.

## Upgrade options

As described above, the syntax for the upgrader with a specified `pe.conf` file is:

~~~
sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
~~~

When you use the `-c` option, the `pe.conf` file name with full path is required. The command will quit with an error message if you omit it.

The following options are also available:

- `-D`: The installer/upgrader will display debugging information.
- `-q`: The installer/upgrader will run in quiet mode. The installation process will not be displayed. If errors occur during the upgrade, the command will quit with an error message.
- `-y`: The installer/upgrader will will run automatically using whatever `pe.conf` is in `/etc/puppetlabs/enterprise/conf.d/`. Note that if this file is incorrect, your upgrade will fail.
- `-V`: The installer/upgrader will display very verbose debugging information.
- `-h`: The installer/upgrader will display help information.


## Upgrading compile masters

If you have any compile masters to upgrade, first upgrade your monolithic installation and then SSH into each Puppet compile master and run the following command:

~~~
curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/upgrade.bash | sudo bash
~~~

## Upgrading Puppet agents

Please see the page on [upgrading Puppet agents](./install_upgrading_agents.html).

## Upgrading Razor

If you have installed Razor in a previous version of PE, you must [upgrade Razor](./razor_upgrade.html) to be in sync with the lastest version of PE.

## Checking for updates

[Check here][updateslink] to find out the latest maintenance release of Puppet Enterprise. To see the version of PE you are currently using, run `puppet --version` on the command line.

{% comment %} This link is the same one as the console's help -> version information link. We only have to change the one to update both. {% endcomment %}

[updateslink]: http://info.puppetlabs.com/download-pe.html

**Note: By default, the Puppet master checks for updates whenever the `pe-puppetserver` service restarts.** As part of the check, it passes some basic, anonymous information to Puppet's servers. This behavior can be disabled if need be. You can find details on what is collected and how to disable checking in the [pe.conf parameter reference](./install_pe_conf_param.html).

## Upgrade options

As described above, the syntax for the upgrader with a specified `pe.conf` file is:

~~~
sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
~~~

When you use the `-c` option, the `pe.conf` file name with full path is required. The command will quit with an error message if you omit it.

The following options are also available:

- `-D`: The installer/upgrader will display debugging information.
- `-q`: The installer/upgrader will run in quiet mode. The installation process will not be displayed. If errors occur during the upgrade, the command will quit with an error message.
- `-y`: The installer/upgrader will will run automatically using whatever `pe.conf` is in `/etc/puppetlabs/enterprise/conf.d/`. Note that if this file is incorrect, your upgrade will fail.
- `-V`: The installer/upgrader will display very verbose debugging information.
- `-h`: The installer/upgrader will display help information.


