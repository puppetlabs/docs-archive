---
layout: default
title: "Upgrading Puppet Enterprise components"
canonical: "/pe/latest/install_upgrading.html"
---


## Before beginning: Review 2016.1 upgrade information

For optimal performance, we recommend the following upgrade path.

Upgrading from    | We recommend upgrades to|
:----------------:|:-----------------------:|
3.8.6, 2016.1.1   | **2016.1.2**            |

[Upgrading Puppet Enterprise: Notes and Warnings](./install_upgrading_notes.html) gathers important information about changes you'll encounter or need to prepare for when arriving to this version of PE.

### On PE 3.8?

If you are on PE 3.8, instead of upgrading, you can follow the [3.8 to 2016.1 migration documentation](./migrate_monolithic.html).

In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions.

### Upgrade overview

The upgrade process consists of the following high-level steps:

1. Review the [upgrade notes](./install_upgrading_notes.html), [new features and enhancements](./release_notes.html), and [known issues](./release_notes_known_issues.html).
2. Download the latest version of PE.
3. Back up your databases and other PE files.
4. Prepare your Puppet code for migration/upgrade.
4. (Optional) Prepare your external PostgreSQL instance for upgrade.
5. Upgrade your monolithic or split installation.
6. Upgrade your Puppet agents.

## Download PE

If you haven't done so already, download a Puppet Enterprise tarball appropriate for your system(s). See the [installing PE][downloading] section of this guide for more information on accessing Puppet Enterprise tarballs, or go directly to the [download page](http://info.puppetlabs.com/download-pe.html).

[downloading]: ./install_basic.html#downloading-puppet-enterprise

After the download completes, copy the appropriate tarball to each node you'll be upgrading. Note that you need about 1 GB of space in `/tmp` to untar the installer.

The PE installer script is used to perform both installations and upgrades. When you run the PE install/upgrade script, it detects any previous versions of PE components and stops any PE services that are currently running. The script then steps through the upgrade process, providing default answers based on the components it has detected on the monolithic PE server. The upgrader should be able to answer all of the questions based on your current installation before starting the upgrade.

As with installation, the script also checks for any missing dependent vendor packages and offers to install them automatically.

Lastly, the script summarizes the upgrade plan and asks you to go ahead and perform the upgrade. Your answers to the script are saved as usual in `/etc/puppetlabs/installer/answers.install`.

The upgrade script runs and provides detailed information as to what it installs, what it updates, and what it replaces. It preserves existing certificates and `puppet.conf` files.

Before starting the upgrade, all of the components in your current deployment should be correctly configured and communicating with each other.

## Back up your databases and other PE files

   Before proceeding with your upgrade, we recommend that you [back up the PE databases and PE files](./maintain_backup_restore.html#back-up-your-database-and-puppet-enterprise-files).

## Prepare your Puppet code for migration/upgrade

PE 3.8.3 introduced compatibility with the Puppet 4 language parser, so you could begin testing your Puppet code against the major changes we made in the Puppet language. We fully enabled these language changes in PE 2015.2, and the [catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview) helps you prepare for migration/upgrade from Puppet 3.8 to the latest version of Puppet Enterprise. See [preparing for migrations and upgrades with the catalog_preview module](./migrate_pe_catalog_preview.html) for a guide to testing your environments with this module before you begin your upgrade.

## Upgrading an external PostgreSQL instance

If you prefer to use an existing instance of PostgreSQL, that instance needs to be manually configured with the correct users and access, and it must be version 9.4.

If you are using an external PostgreSQL instance, please note the following:

1. If you have not already done so, you need to create databases for RBAC, activity service, and the node classifier before upgrading.

2. If you have not already done so, you need to enable the citext extension on the RBAC database, and the the `pg_trim` and `pgcrypto` extensions on the PuppetDB database.

These tasks should be done **BEFORE** starting the upgrade.

[External PostgreSQL prep notes](./install_pe_split.html#external-postgresql-prep-notes) provides more information about setting up these databases and extensions.

Finally, please review these [important notes about PostgresSQL upgrades](./install_upgrading_notes.html#important-notes-about-postgresql-upgrades).

## Upgrading a monolithic installation

If you have a monolithic installation (with the master, PE console, and database components all on the same node), the process involves the following steps, which **must be performed in the following order**:

1. [Prepare your Puppet code for migration/upgrade](#prepare-your-puppet-code-for-migrationupgrade)
2. [Upgrade the monolithic PE server](#upgrade-the-monolithic-pe-server)
3. [Upgrade Puppet agent nodes](./install_upgrading_agents.html)

### Upgrade the monolithic PE server

> **Important:** All installer commands should be run as `root`.

**Before you begin:** If your master is set to noop mode (`noop = true` in `puppet.conf` on the master), remove or comment out this flag prior to upgrade. The installer fails if you attempt to upgrade in noop mode.

**To start the upgrade**:

1. If necessary, review [Upgrading an external PostgreSQL instance](#upgrading-an-external-postgresql-instance).
2. (**Optional**) Prepare an [answer file](./install_automated.html) with the following content:

   ~~~
   q_use_application_services=y
   q_install=y
   ~~~

   If you don't wish to enable application orchestration services at this time, you can skip this step and run the upgrader without an answer file. However, to prevent issues when upgrading in the future, you will need to manually add the [PE Orchestrator group](./console_classes_groups_preconfigured_groups.html#the-pe-orchestrator-node-group) in the PE console.

3. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
4. From the installer directory, run `sudo ./puppet-enterprise-installer`.

   If you are enabling application orchestration services at this time, run `sudo ./puppet-enterprise-installer -a <PATH TO ANSWER FILE>`.

### Upgrade Puppet agents

Please see the page on [upgrading Puppet agents](./install_upgrading_agents.html).

##  Upgrading a split installation

If you have a split installation (with the master, PE console, and database components on different nodes), the process involves the following steps, which **must be performed in the following order**:

1. [Prepare your Puppet code for migration/upgrade](#prepare-your-puppet-code-for-migrationupgrade)
2. [Upgrade the Puppet Master](#upgrade-the-puppet-master)
3. [Upgrade PuppetDB](#upgrade-puppetdb)
4. [Upgrade the PE Console](#upgrade-the-pe-console)
5. [Upgrade Puppet agent nodes](#upgrade-puppet-agents)

>**Important warning about `auth.conf`**: If you have a custom legacy `auth.conf` on your Puppet master in a split installation and you want to continue using it (you've set `q_migrate_auth_conf_after_upgrade=n` or answered similarly when prompted by the installer), you need to set the `q_migrate_auth_conf_after_upgrade=n` when upgrading your console node as well.
>
> For more information about this issue, refer to [Changes to `auth.conf` when Changes to `auth.conf` when upgrading from the 3.8.x series](./install_upgrading_notes.html#changes-to-authconf-when-upgrading-from-the-38x-series).

**Before you begin:** If your master is set to noop mode (`noop = true` in `puppet.conf` on the master), remove or comment out this flag prior to upgrade. The installer fails if you attempt to upgrade in noop mode.

### Upgrade the Puppet master

> **Important:** All installer commands should be run as `root`.

**To start the upgrade on the Puppet master**:

 1. (**Optional**) Prepare an [answer file](./install_automated.html) with the following content:

    ~~~
    q_use_application_services=y
    q_install=y
    ~~~

    If you don't wish to enable application orchestration services at this time, you can skip this step and run the upgrader without an answer file. However, to prevent issues when upgrading in the future, you will need to manually add the [PE Orchestrator group](./console_classes_groups_preconfigured_groups.html#the-pe-orchestrator-node-group) in the PE console.

2. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. From the installer directory, run `sudo ./puppet-enterprise-installer`.

   If you are enabling application orchestration services at this time, run `sudo ./puppet-enterprise-installer -a <PATH TO ANSWER FILE>`.
4. Navigate to `/etc/puppetlabs/installer/database_info.2015.3.3.upgrade` and copy the database name, username, and password for the pe-orchestration database.

   You will need to supply this information when you run the installer to upgrade the PuppetDB component.

5. Continue to upgrade PuppetDB.

### Upgrade PuppetDB

**To start the upgrade on PuppetDB**:

1. If necessary, review [Upgrading an external PostgreSQL instance](#upgrading-an-external-postgresql-instance).
2. Transfer the PE installation/upgrade tarball to the PuppetDB node.
3. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
4. From the installer directory, run `sudo ./puppet-enterprise-installer`.
5. (**If you're upgrading from 3.8.5**) When prompted by the installer, supply the database name, username, and password for the pe-orchestration database.

### Upgrade the PE console

**To start the upgrade on the PE console**:

1. Transfer the PE installation/upgrade tarball to the PE console node.
2. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. From the installer directory, run `sudo ./puppet-enterprise-installer`.

   The installer also asks for the following information:

   * The hostname and port number for the PuppetDB node you created in the previous step.
   * Database credentials; specifically, the database names, user names, and passwords for the console, role-based access control (RBAC), and PuppetDB databases. These can be found in `/etc/puppetlabs/installer/database_info.install` on the PuppetDB node.

> **Note**: The node classifier and role-based access control (RBAC) are installed on the PE console node.

### Upgrade Puppet agents

Please see the page on [upgrading Puppet agents](./install_upgrading_agents.html).

## Upgrading large environment installations

Refer to [upgrading a large environment installation (LEI)](./install_lei_upgrade.html) for instructions on upgrading your LEI, including compile masters.

## Upgrading Razor

If you have installed Razor in a previous version of PE, you must [upgrade Razor](./razor_upgrade.html) to be in sync with the lastest version of PE.

## Checking for updates

[Check here][updateslink] to find out the latest maintenance release of Puppet Enterprise. To see the version of PE you are currently using, run `puppet --version` on the command line.

{% comment %} This link is the same one as the console's help -> version information link. We only have to change the one to update both. {% endcomment %}

[updateslink]: http://info.puppetlabs.com/download-pe.html

**Note: By default, the Puppet master checks for updates whenever the `pe-puppetserver` service restarts.** As part of the check, it passes some basic, anonymous information to Puppet's servers. This behavior can be disabled if need be. You can find details on what is collected and how to disable checking in the [answer file references](./install_mono_answers.html#puppet-master-answers).


