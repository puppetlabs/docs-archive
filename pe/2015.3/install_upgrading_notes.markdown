---
layout: default
title: "Upgrading Puppet Enterprise: Notes and Warnings"
canonical: "/pe/latest/install_upgrading_notes.html"
---

>**Note**: A complete list of known issues is provided in the [PE 2015.3 release notes](./release_notes_known_issues.html). Please review this list before upgrading.


## Upgrading to PE 2015.3 From 3.8.3?

The following changes were made in the PE 2015.2 series. If you're upgrading from 3.8.3, please review these items before upgrading.

### Major Changes to Configuration Files and Directories

In PE 2015.2 (and Puppet 4.2), we changed the locations of several important configuration files and directories. For a summary of the changes, see [Major Path Changes in PE 2015.3](./install_upgrading_path_changes.html).

### Important Notes About PostgreSQL Upgrades

- If you are upgrading or installing PE 2015.3, and use an external PostgreSQL instance, you must first ensure that it is [upgraded to or running version 9.4](http://www.postgresql.org/docs/9.4/static/upgrading.html). During the installation/upgrade, if we find that the external PostgreSQL database is any other version, the installer/upgrader will quit.

- If you have a monolithic or split installation with a PE-managed PostgreSQL instance, before upgrading verify that your PostgreSQL version is 9.2 (the only version supported).

- For upgrades of PE-managed PostgreSQL instances, the machine hosting the instance must have a hard drive with at least three times more free space than the amount of space consumed by existing Puppet data.

- During the upgrade of a PE-managed PostgreSQL instance, we will verify that it has been properly upgraded to 9.4 and that the `pe-postgres` is running before we delete the any PostgreSQL 9.2 data files. (Note: a full backup is created during upgrade.)

- During the upgrade of a PE-managed PostgreSQL instance, we will create a backup file. This file will remain on your system when the upgrade completes. The file may be quite large, so after you've established that your upgrade was successful and your Puppet master is working correctly, delete it or transfer it to another location. For a monolithic install, the backup file can be recovered from the Puppet master node at `/opt/puppet/var/lib/pgsql/9.2/`. For a split install, the backup file can be recovered from the PuppetDB node at at the same location.

- If you're hosting non-Puppet data on your PostgreSQL instance, take extra care to ensure that it's backed up and migrated correctly. We don't support migration of non-Puppet data.

- If you're upgrading an external PostgreSQL instance, and have not already set up the databases and extensions required by PE, [External PostgreSQL Option and Prep Notes](./install_system_requirements.html#external-postgresql-option-and-prep-notes) provides information about setting up these databases and extensions.

- PE 2015.2 eliminated use of the console database (`pe-console`). During upgrades of PE-managed PostrgreSQL databases, we remove the console database. However, if you're using an external PostgreSQL instance, we will not remove the Console database. You can remove this database or leave it in place---it will not affect your system if left in place. If you're upgrading from PE 3.8.3, the backup of the console database can be found at `/opt/puppet/var/lib/pgsql/3.8.3_backups/console.sql` (on the database node in a split install).

### New Puppet Language Parser

PE 2015.2 included major revisions to the Puppet language and, consequently, the language parser (introduced for testing in PE 3.8). Before upgrading, ensure your Puppet code is compatible with the new parser (previously referred to as the "future parser").

If you're upgrading from PE 3.8.3, you can check your Puppet code against the new parser using the [Puppetlabs catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview). This module allows you to use the Puppet 4 parser in a test environment to ensure it works with your existing Puppet code.

* The `enable_future_parser` parameter is deprecated. If you enabled the Puppet 4 language parser in PE 3.8.x via the console, Hiera, or a third-party classification tool, **after upgrading** you must [use the console to remove](./console_classes_groups_making_changes.html#deleting-parameters) the paramater from the `puppet_enterprise::profile::master` class.
* The `parser=future` setting is no longer valid. If you used this setting in any `environment.conf` files to enable the parser in PE 3.8.x, you'll see warning messages during your upgrade. After you upgrade, remove this setting from any `environment.conf` files.

### Tracking Unchanged Events When Upgrading to PE 2015.3 from PE 3.8.x

With PE 2015.2, the console database was eliminated and replaced with Puppet DB. If you're upgrading to PE 2015.3, this change should be somewhat seamless. However, if you want to keep track of your unchanged events while you upgrade from PE 3.8.x to PE 2015.3, for example, if you need a 14 or 30 day lookback, then you must keep a version of PE 3.8.x running in parallel for the necessary number of days until the new database is populated with information.

### PE 3.8.x Agents Will Not Have MCollective and SymLinks Managed

Due to the way that the `pe_version` fact works in PE 2015.3, agents running 3.8.x against a PE 2015.3 Puppet master will not be classified in the PE Mcollective and PE Agents groups. Because of this, MCollective and symlinks will not be managed for these agents. We recommend you don't change any symlinks or MCollective configurations on these agents.

### Nginx Replaced Apache as the Puppet Enterprise Web Server

In PE 2015.2, nginx replaced Apache as the PE webserver. This change was made to enhance security and performance in this release and future releases of PE. This change should be mostly transparent to users, except for the following:

- The webserver user changes from `pe-apache` (`pe-httpd`) to `pe-webserver`.
- The webserver service changes from `pe-httpd` to `pe-nginx`.

## Upgrading to Puppet Enterprise 2015.3 Requires Additional Steps to Configure the pe-orchestration Database

>**Note**: This applies to split installations only.

PE 2015.3 includes a new database, the pe-orchestration database, for the Application Orchestration service. Since the pe-orchestration services runs on the Puppet master, the credentials for this database need to be available to the Puppet master. After you upgrade the Puppet master, you need to navigate to `/etc/puppetlabs/installer/database_info.2015.3.1.upgrade` and copy the database name, user name, and password the PE installer provided for the Orchestration database.

If you want to supply your own username, database name, and password, you can pass in an answer file with the following questions:

- `q_orchestrator_database_name=` (`pe-orchestrator` is the default)
- `q_orchestrator_database_password=<Your Password>`
- `q_orchestrator_database_user=` (`pe-orchestrator` is the default)

When you run the installer on the PuppetDB component, provide the credentials for the Orchestrator database when prompted. The [upgrade instructions](./install_upgrading.html#upgrading-a-split-installation) reflect these additional steps.

## Changes to `auth.conf` When Upgrading to Puppet Enterprise 2015.3

For versions of PE previous to 2015.3, the [`auth.conf`](/puppet/4.3/reference/config_file_auth.html) file managed access to Puppet's HTTPS API. This file was located at `/etc/puppetlabs/puppet/auth.conf` on the Puppet master.

PE 2015.3 introduces a new `auth.conf` (`/etc/puppetlabs/puppetserver/conf.d/auth.conf`)that makes use of Puppet Server's trapper-keeper services <!--NOTE: Add link to Puppet Server docs-->.

We recommend that you address the changes, as some functionality (e.g., console services) may not be available after upgrade if the endpoints aren't authorized.

### Choose Path for Upgrading With Custom `auth.conf`

When upgrading to PE 2015.3 with a custom auth.conf file you have two choices:

- You can continue to use your legacy `auth.conf` file, but you must update it to include the endpoints introduced in the 2015.3 series. You must acknowledge you're aware of the differences when prompted by the upgrader, or pass in an answer file with `q_exit_and_update_auth_conf=n` to continue with the upgrade.
- You can upgrade to 2015.3 and then modify the new `auth.conf` file to include any customizations from your legacy file. To do this you can pass in an answer file with `q_migrate_auth_conf_after_upgrade=y`.

   >**Important**: If you have a custom legacy `auth.conf` on your Puppet master in a split installation and you want to continue using it (you've set `q_migrate_auth_conf_after_upgrade=n` or answered similarly when prompted by the installer), you need to set the `q_migrate_auth_conf_after_upgrade=n` when upgrading your console node as well.

### Flag to Toggle 2015.3 `auth.conf` In PE Master Class

The `puppet_enterprise::profile::master` class in the **PE Master** node group contains a new flag, `use_legacy_auth_conf`. You can set this flag to `true` to use the legacy `auth.conf` file, but be sure you address the endpoints we've added since the 3.8.x series.

### Notable Changes and Additions Made to Legacy `auth.conf` Since 3.8.x

Here are some notable changes that have been made to the endpoints in `auth.conf` between the 3.8.x and PE 2015.2 versions:

3.8 Endpoint Route | 2015.2 Endpoint Route
----------|-------------------------------
`/catalog/` | `/puppet/v3/catalog/`
`/node/`    | `/puppet/v3/node/`
`/report/`  | `/puppet/v3/report/`
`/file/`  | `/puppet/v3/file/`
`/v2.0/environments` | `/puppet/v3/environments`
`/facts/`   | `/puppet/v3/facts/`
`/resource_type/` | `/puppet/v3/resource_type/`
`/certificate/`   | `/puppet-ca/v1/certificate/`
`/certificate_request` | `/puppet-ca/v1/certificate_request`
`/certificate/ca` | `/puppet-ca/v1/certificate/ca`
`/certificate_revocation_list/ca` | `/puppet-ca/v1/certificate_revocation_list/ca`

The 2105.2 series also added in the status route at `/puppet/v3/status`.

The 2015.3 series added in the environment route at `/puppet/v3/environment`

The 2015.3 series also requires that the pe-internal-orchestrator certificate be added to both the `/puppet/v3/environment` and `/puppet/v3/resource_type` routes.

### Notable Changes and Additions in the 2015.3 `auth.conf`

The 2015.3 series added in the environment route at `/puppet/v3/environment`.

The 2015.3 series also requires that the `pe-internal-orchestrator` certificate be added to both the `/puppet/v3/environment` and `/puppet/v3/resource_type` routes.

Refer to the [Puppet Server 2.2 release notes](/puppetserver/2.2/release_notes.html) for more information about the new `auth.conf` file format.

## Hard Tabs For Indentation In Hiera YAML Files Cause Errors After Upgrading

If you're upgrading to the PE 2015.3 series, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading.

## Customized PE Initialization Script Defaults Are Not Replaced on Upgrade

If you customized the `pe-console-services`, `pe-puppetserver`, or `pe-puppetdb` initialization scripts in `/etc/sysconfigs` (on RHEL-based systems) or `/etc/defaults` (on Debian-based systems) the customizations will be lost when you upgrade. This is mainly due to major path changes between versions. However, please note that the customized scripts will be saved in the same location with a `.bak` extension.

## Before Upgrading, Correct Invalid Entries in `autosign.conf`

The upgrade will not configure the PE console if any entries in `/etc/puppetlabs/puppet/autosign.conf` don't conform to the [autosign requirements](/puppet/4.2/reference/ssl_autosign.html#the-autosignconf-file). Please correct any invalid entries before upgrading to PE 2015.3.
