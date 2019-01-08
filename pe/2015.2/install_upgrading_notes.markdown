---
layout: default
title: "PE 2015.2 » Installing » Upgrading (Important Notes and Warnings)"
subtitle: "Upgrading Puppet Enterprise: Notes and Warnings"
canonical: "/pe/latest/install_upgrading_notes.html"
---

>**Note**: A complete list of known issues is provided in the [PE 2015.2 release notes](./release_notes_known_issues.html). Please review this list before upgrading.


## Major Changes to Configuration Files and Directories

In PE 2015.2.x (and Puppet 4.2), we changed the locations of several important configuration files and directories. For a summary of the changes since PE 3.8.x, see [Major Path Changes in PE 2015.2](./install_upgrading_path_changes.html).

### Directory Environments Permanently Enabled

In this release, [directory environments are always on](./install_upgrading_path_changes.html#directory-environments-are-always-on). If you're upgrading from 3.8.x and the installer detects a non-directory environment, the upgrade will fail, and you will need to correct the issue. 

## Important Notes About PostgreSQL Upgrades

- If you are upgrading or installing PE 2015, and use an external PostgreSQL instance, you must first ensure that it is [upgraded to or running version 9.4](http://www.postgresql.org/docs/9.4/static/upgrading.html). During the installation/upgrade, if we find that the external PostgreSQL database is any other version, the installer/upgrader will quit.

- If you have a monolithic or split installation with a PE-managed PostgreSQL instance, before upgrading verify that your PostgreSQL version is 9.2 (the only version supported).

- For upgrades of PE-managed PostgreSQL instances, the machine hosting the instance must have a hard drive with at least three times more free space than the amount of space consumed by existing Puppet data.

- During the upgrade of a PE-managed PostgreSQL instance, we will verify that it has been properly upgraded to 9.4 and that the `pe-postgres` is running before we delete the any PostgreSQL 9.2 data files. (Note: a full backup is created during upgrade.)

- During the upgrade of a PE-managed PostgreSQL instance, we will create a backup file. This file will remain on your system when the upgrade completes. The file may be quite large, so after you've established that your upgrade was successful and your Puppet master is working correctly, delete it or transfer it to another location. For a monolithic install, the backup file can be recovered from the Puppet master node at `/opt/puppet/var/lib/pgsql/pg_backup.9.2.sql`. For a split install, the backup file can be recovered from the PuppetDB node at `/opt/puppet/var/lib/pgsql/pg_backup.9.2.sql`.

- If you're hosting non-Puppet data on your PostgreSQL instance, take extra care to ensure that it's backed up and migrated correctly. We don't support migration of non-Puppet data.

- PE 2015.2 eliminated the console database (`pe-console`). During upgrades of PE-managed PostrgreSQL databases, we remove the console database. However, if you're using an external PostgreSQL instance, we will not remove the console database. You can remove this database or leave it in place. It will not affect your system if left in place.

## New Puppet Language Parser

PE 2015.2.x includes major revisions to the Puppet language and, consequently, the language parser (introduced for testing in PE 3.8). Before upgrading, ensure your Puppet code is compatible with the new parser (previously referred to as the "future parser").

You can check your Puppet code against the new parser using the [Puppetlabs catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview). This module allows you to use the Puppet 4 parser in a test environment to ensure it works with your existing Puppet code.

* The `enable_future_parser` parameter is deprecated. If you enabled the Puppet 4 language parser in PE 3.8.x via the console, Hiera, or a third-party classification tool, **after upgrading** you must [use the console to remove](./console_classes_groups_making_changes.html#deleting-parameters) the paramater from the `puppet_enterprise::profile::master` class. 
* The `parser=future` setting is no longer valid. If you used this setting in any `environment.conf` files to enable the parser in PE 3.8.x, you'll see warning messages during your upgrade. After you upgrade, remove this setting from any `environment.conf` files.

## PE 3.8.x Agents Will Not Have MCollective and SymLinks Managed

Due to the way that the `pe_version` fact works in PE 2015.2, agents running 3.8.x against a PE 2015.2 Puppet master will not be classified in the PE Mcollective and PE Agents groups. Because of this, MCollective and symlinks will not be managed for these agents. We recommend you don't change any symlinks or MCollective configurations on these agents.

## Nginx Replaces Apache as the Puppet Enterprise Web Server

In PE 2015.2.x, nginx replaces Apache as the PE webserver. This change was made to enhance security and performance in this release and future releases of PE. This change should be mostly transparent to users, except for the following:

- The webserver user changes from `pe-apache` (`pe-httpd`) to `pe-webserver`.
- The webserver service changes from `pe-httpd` to `pe-nginx`.

## Upgrading to Puppet Enterprise 2015.2 With a Modified `auth.conf` File

The [`auth.conf`](/puppet/4.2/reference/config_file_auth.html) file manages access to Puppet's HTTPS API. This file is located at `/etc/puppetlabs/puppet/auth.conf` on the Puppet master.

If we find a modified `auth.conf` during the 2015.2 upgrade process, we will attempt to create a diff between your modified version and the 2015.2 version (reliant on the presence of a diff executable on your Puppet master server). You may need to modify your `auth.conf` to address the differences, as this file is not managed by PE. We recommend that you consider and address the changes, as some functionality (e.g., console services) may not be available after upgrade if the endpoints aren't authorized.

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

We've also added in the status route at `/puppet/v3/status`.

You must acknowledge you're aware of the differences when prompted by the upgrader, or pass in an answer file with `q_exit_and_update_auth_conf` in it when running the upgrade.

## Hard Tabs For Indentation In Hiera YAML Files Cause Errors After Upgrading

If you're upgrading to the PE 2015.2.x series, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading. 

## Customized PE Initialization Script Defaults Are Not Replaced on Upgrade

If you customized the `pe-console-services`, `pe-puppetserver`, or `pe-puppetdb` initialization scripts in `/etc/sysconfigs` (on RHEL-based systems) or `/etc/defaults` (on Debian-based systems) the customizations will be lost when you upgrade. This is mainly due to major path changes between versions. However, please note that the customized scripts will be saved in the same location with a `.bak` extension.

## Before Upgrading, Correct Invalid Entries in `autosign.conf`

The upgrade will not configure the PE console if any entries in `/etc/puppetlabs/puppet/autosign.conf` don't conform to the [autosign requirements](/puppet/4.2/reference/ssl_autosign.html#the-autosignconf-file). Please correct any invalid entries before upgrading to PE 2015.2.
