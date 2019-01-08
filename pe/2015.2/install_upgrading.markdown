---
layout: default
title: "PE 2015.2» Installing » Upgrading"
subtitle: "Upgrading Puppet Enterprise"
canonical: "/pe/latest/install_upgrading.html"
---


## Before Beginning: Review 2015.2.3 Upgrade Information

For optimal performance, we recommend the following upgrade path. 

Upgrading from    | We recommend upgrades to|
:----------------:|:-----------------------:|
3.8.3             | **2015.2.3**            |

### On PE 3.8?
 
If you are on PE 3.8, instead of upgrading, you can follow the [3.8 to 2015.2 migration documentation](./migrate_monolithic.html).

In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions.

## Download PE

If you haven't done so already, download a Puppet Enterprise tarball appropriate for your system(s). See the [Installing PE][downloading] section of this guide for more information on accessing Puppet Enterprise tarballs, or go directly to the [download page](http://info.puppetlabs.com/download-pe.html).

[downloading]: ./install_basic.html#downloading-puppet-enterprise

Once downloaded, copy the appropriate tarball to each node you'll be upgrading. Note that you need about 1 GB of space in `/tmp` to untar the installer.

The Puppet Installer script is used to perform both installations and upgrades. The script checks for a prior version and runs as upgrader or installer as needed. You start by [downloading][downloading] and unpacking a tarball with the appropriate version of the PE packages for your system. Then, when you run the `puppet-enterprise-installer` script, the script checks for a prior installation of PE and, if it detects one, asks if you want to proceed with the upgrade. The installer then upgrades all the PE components (master, agent, etc.) it finds on the node to version 2015.2.

Before starting the upgrade, all of the components (agents, master, console, etc.) in your current deployment should be correctly configured and communicating with each other.

## Back Up Your Databases and Other PE Files

   Before proceeding with your upgrade, we recommend that you [back up the PE databases and PE files](./maintain_backup_restore.html#back-up-your-database-and-puppet-enterprise-files).

## Prepare Your Puppet Code for Migration/Upgrade to PE 2015.2.x

PE 3.8.1 introduced compatibility with the Puppet 4 language parser, so you could begin testing your Puppet code against the major changes we made in the Puppet language. We've fully enabled these language changes in PE 2015.2, and the [catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview) helps you prepare for migration/upgrade from Puppet 3.8.1 to Puppet Enterprise 2015.2. See [Preparing for Migration to PE 2015.2](./migrate_pe_catalog_preview.html) for a guide to testing your environments with this module before you begin your upgrade.

## Upgrading a Monolithic Installation

If you have a monolithic installation (with the master, PE console, and database components all on the same node), the process involves the following steps, which **must be performed in the following order**:

1. [Prepare Your Puppet Code for Migration/Upgrade to PE 2015.2](#prepare-your-puppet-code-for-migration-upgrade-to-pe20152)
2. [Upgrade the monolithic PE server](#upgrade-the-monolithic-pe-server)
3. [Upgrade Puppet agent nodes](./install_upgrading_agents.html)

### Upgrade the Monolithic PE Server

> **Important:** All installer commands should be run as `root`.

**To start the upgrade**:

1. Unpack the PE 2015.2.3 tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
2. From the installer directory, run `sudo ./puppet-enterprise-installer`.

The install script detects any previous versions of PE components and stops any PE services that are currently running. The script then steps through the install script, providing default answers based on the components it has detected on the monolithic PE server. The upgrader should be able to answer all of the questions based on your current installation before starting the upgrade.

As with installation, the script also checks for any missing dependent vendor packages and offers to install them automatically.

Lastly, the script summarizes the upgrade plan and asks you to go ahead and perform the upgrade. Your answers to the script are saved as usual in `/etc/puppetlabs/installer/answers.install`.

The upgrade script runs and provides detailed information as to what it installs, what it updates, and what it replaces. It preserves existing certificates and `puppet.conf` files.

> **Note**: The node classifier and role-based access control (RBAC) are installed on the PE console node.

### Upgrade Puppet Agents

Please see the page on [upgrading Puppet agents](./install_upgrading_agents.html).

##  Upgrading a Split Installation

If you have a split installation (with the master, PE console, and database components on different nodes), the process involves the following steps, which **must be performed in the following order**:

1. [Prepare Your Puppet Code for Migration/Upgrade to PE 2015.2](#prepare-your-puppet-code-for-migration-upgrade-to-pe20152)
2. [Upgrade the Puppet Master](#upgrade-the-puppet-master)
3. [Upgrade PuppetDB](#upgrade-puppetdb)
4. [Upgrade the PE Console](#upgrade-the-pe-console)
5. [Upgrade Puppet agent nodes](./install_upgrading_agents.html)

> **Note**: The node classifier and role-based access control (RBAC) will be installed on the PE console node.

> ![windows logo](./images/windows-logo-small.jpg) To upgrade Windows agents, download and run the new MSI package as described in [Installing Windows Agents](./install_windows.html). However, be sure to upgrade your master, console, and database nodes first.

### Upgrade the Puppet Master

> **Important:** All installer commands should be run as `root`.

**To start the upgrade on the Puppet master**:

1. Unpack the PE 2015.2.3 tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
2. From the installer directory, run `sudo ./puppet-enterprise-installer`.

The install script detects any previous versions of PE components and stops any PE services that are currently running. The script then steps through the install script, providing default answers based on the components it has detected on the node (e.g., if the script detects only an agent on a given node, it provides "No" as the default answer to installing the master component). The upgrader should be able to answer all of the questions based on your current installation except for the hostname and port of the PuppetDB node you prepped before starting the upgrade.

As with installation, the script also checks for any missing dependent vendor packages and offers to install them automatically.

Lastly, the script summarizes the upgrade plan and asks you to go ahead and perform the upgrade. Your answers to the script are saved as usual in `/etc/puppetlabs/installer/answers.install`.

The upgrade script runs and provides detailed information as to what it installs, what it updates, and what it replaces. It preserves existing certificates and `puppet.conf` files.

### Upgrade PuppetDB

> **Note**: The upgrader can install a pre-configured version of PostgreSQL (must be version 9.4) along with PuppetDB on the node you select. If you prefer to use a node with an existing instance of PostgreSQL, that instance needs to be manually configured with the correct users and access. This also needs to be done **BEFORE** starting the upgrade.
>
> In addition, before beginning, please review [Important Notes About PostgresSQL Upgrades](./install_upgrading_notes.html#important-notes-about-postgresql-upgrades).

**To start the upgrade on PuppetDB**:

1. Transfer the PE 2015.2.3 tarball to the PuppetDB node.
2. Unpack the PE 2015.2.3 tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. From the installer directory, run `sudo ./puppet-enterprise-installer`.(Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)

### Upgrade the PE Console

**To start the upgrade on the PE console**:

1. Transfer the PE 2015.2.3 tarball to the PE console node.
2. Unpack the PE 2015.2.3 tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. From the installer directory, run `sudo ./puppet-enterprise-installer`.(Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)

The installer detects the version from which you are upgrading and answers as many installer questions as possible based on your existing deployment.

The installer also asks for the following information:

* The hostname and port number for the PuppetDB node you created in the previous step.
* Database credentials; specifically, the database names, user names, and passwords for the console, role-based access control (RBAC), and PuppetDB databases. These can be found in `/etc/puppetlabs/installer/database_info.install` on the PuppetDB node.

> **Important**: If you will use your own instance of PostgreSQL (as opposed to the instance PE can install) for the console and PuppetDB, it must be version 9.4.

In addition if you are using an external PostgreSQL instance that is not managed by PE, please note the following:

1. You'll need to create databases for RBAC, activity service, and the node classifier before upgrading.
2. The databases you create need to have the citext extension enabled.

### Upgrade Puppet Agents

Please see the page on [upgrading Puppet agents](./install_upgrading_agents.html).

## Upgrading Large Environment Installations

Refer to [upgrading a large environment installation (LEI)](./install_lei_upgrade.html) for instructions on upgrading your LEI, including compile masters. 

### Upgrading Razor

If you have installed Razor in a previous version of PE, you must [upgrade Razor](./razor_upgrade.html) to be in sync with PE 2015.2.

## Checking For Updates

[Check here][updateslink] to find out the latest maintenance release of Puppet Enterprise. To see the version of PE you are currently using, run `puppet --version` on the command line.

[updateslink]: http://info.puppetlabs.com/download-pe.html

**Note: By default, the Puppet master checks for updates whenever the `pe-puppetserver` service restarts.** As part of the check, it passes some basic, anonymous information to Puppet Labs' servers. This behavior can be disabled if need be. You can find details on what is collected and how to disable checking in the [answer file references](./install_mono_answers.html#puppet-master-answers).

* * *


- [Next: Uninstalling](./install_uninstalling.html)
