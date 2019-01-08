---
layout: default
title: "Upgrading Puppet Enterprise: Important notes"
canonical: "/pe/latest/install_upgrading_notes.html"
---

>**Note**: A complete list of known issues is provided in the [release notes](./release_notes_known_issues.html). Please review this list before upgrading.


## May need to enable file sync after upgrading 

If you were using file sync *without* Code Manager in a previous version of PE, you might need to turn file sync back on after upgrading to 2016.1.1. See the [file sync](./cmgmt_filesync) documentation for instructions. Note that using file sync without Code Manager is an unsupported workflow.

## Upgrading to PE 2016.1.2 from 3.8.5?

If you're upgrading from 3.8.5, please review these items before upgrading.

### Static catalogs enabled by default

A *static* catalog includes metadata that specifies the desired state of any [file resources]({{puppet}}/types/file.html) on a node that has `source` attributes pointing to `puppet:///` locations. This metadata can refer to a specific version of the file, rather than the latest version, and can confirm that the agent is applying the appropriate version of the file resource for the catalog. Also, because the metadata is provided in the catalog rather than calculated by the agent, Puppet runs on the agent finish sooner.

In PE, static catalogs are disabled across all environments for new installations. To use static catalogs in PE, **you must** [enable file sync](./cmgmt_filesync.html#enabling-or-disabling-file-sync). See [Static Catalogs in Puppet Enterprise](./static_catalogs.html) for more information.


### Major changes to configuration files and directories

In PE 2015.2 (and Puppet 4.2), we changed the locations of several important configuration files and directories. For a summary of the changes, see [major path changes in PE 2015.2](./install_upgrading_path_changes.html).

### Important notes about PostgreSQL upgrades

- If you are upgrading or installing PE, and use an external PostgreSQL instance, you must first ensure that it is [upgraded to or running version 9.4](http://www.postgresql.org/docs/9.4/static/upgrading.html). During the installation/upgrade, if we find that the external PostgreSQL database is any other version, the installer/upgrader will quit.

- If you have a monolithic or split installation with a PE-managed PostgreSQL instance, before upgrading verify that your PostgreSQL version is 9.2 (the only version supported).

- For upgrades of PE-managed PostgreSQL instances, the machine hosting the instance must have a hard drive with at least three times more free space than the amount of space consumed by existing Puppet data.

- During the upgrade of a PE-managed PostgreSQL instance, we will verify that it has been properly upgraded to 9.4 and that the `pe-postgres` is running before we delete the any PostgreSQL 9.2 data files. (Note: a full backup is created during upgrade.)

- During the upgrade of a PE-managed PostgreSQL instance, we will create a backup file. This file will remain on your system when the upgrade completes. The file may be quite large, so after you've established that your upgrade was successful and your Puppet master is working correctly, delete it or transfer it to another location. For a monolithic install, the backup file can be recovered from the Puppet master node at `/opt/puppet/var/lib/pgsql/pg_backup.9.2.sql`. For a split install, the backup file can be recovered from the PuppetDB node at `/opt/puppet/var/lib/pgsql/pg_backup.9.2.sql`.

- If you're hosting non-Puppet data on your PostgreSQL instance, take extra care to ensure that it's backed up and migrated correctly. We don't support migration of non-Puppet data.

- If you're upgrading an external PostgreSQL instance, and have not already set up the databases and extensions required by PE, [External PostgreSQL Option and Prep Notes](./install_system_requirements.html#external-postgresql-option-and-prep-notes) provides information about setting up these databases and extensions.

- PE 2015.2 eliminated use of the console database (`pe-console`). During upgrades of PE-managed PostrgreSQL databases, we remove the console database. However, if you're using an external PostgreSQL instance, we will not remove the Console database. You can remove this database or leave it in place---it will not affect your system if left in place.

### New Puppet language parser

PE 2015.2 included major revisions to the Puppet language and, consequently, the language parser (introduced for testing in PE 3.8). Before upgrading, ensure your Puppet code is compatible with the new parser (previously referred to as the "future parser").

If you're upgrading from PE 3.8.5, you can check your Puppet code against the new parser using the [Puppet catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview). This module allows you to use the Puppet 4 parser in a test environment to ensure it works with your existing Puppet code.

* The `enable_future_parser` parameter is deprecated. If you enabled the Puppet 4 language parser in PE 3.8.x via the console, Hiera, or a third-party classification tool, **after upgrading** you must [use the console to remove](./console_classes_groups_making_changes.html#deleting-parameters) the paramater from the `puppet_enterprise::profile::master` class. 
* The `parser=future` setting is no longer valid. If you used this setting in any `environment.conf` files to enable the parser in PE 3.8.x, you'll see warning messages during your upgrade. After you upgrade, remove this setting from any `environment.conf` files.

### Nginx replaced Apache as the Puppet Enterprise web server

Nginx has replaced Apache as the PE webserver. This change was made to enhance security and performance. This change should be mostly transparent to users, except for the following:

- The webserver user changes from `pe-apache` (`pe-httpd`) to `pe-webserver`.
- The webserver service changes from `pe-httpd` to `pe-nginx`.

### Changes to `auth.conf` when upgrading from the 3.8.x series

For versions of PE previous to 2016.1.1, the [`auth.conf`]({{puppet}}/config_file_auth.html) file managed access to Puppet's HTTPS API. This file was located at `/etc/puppetlabs/puppet/auth.conf` on the Puppet master.

We've introduced a new `auth.conf` (`/etc/puppetlabs/puppetserver/conf.d/auth.conf`) that makes use of Puppet Server's trapper-keeper services.  

We recommend that you address the changes, as some functionality (e.g., console services) may not be available after upgrade if the endpoints aren't authorized.

**When upgrading to PE 2016.1.1 from PE 3.8, you have two choices**:

- You can continue to use your legacy `auth.conf` file, but you must update it to include the endpoints introduced in the both the 2015.2 and 2015.3 series. You must acknowledge you're aware of the differences when prompted by the upgrader, or pass in an answer file with `q_exit_and_update_auth_conf=y`.
- You can upgrade to 2016.1.1 and then modify the new `auth.conf` file to include any customizations from your legacy file. To do this you can pass in an answer file with `q_migrate_auth_conf_after_upgrade=y`.

**When upgrading to PE 2016.1.1 from PE 2015.3, you have two choices**:

- You can continue to use your legacy `auth.conf` file as is. Set `q_migrate_auth_conf_after_upgrade=n` or answer similarly when prompted by the installer.

   >**Important**: If you have a custom legacy `auth.conf` on your Puppet master in a split installation and you want to continue using it (you've set `q_migrate_auth_conf_after_upgrade=n` or answered similarly when prompted by the installer), you need to set the `q_migrate_auth_conf_after_upgrade=n` when upgrading your console node as well. 

- You can upgrade to 2016.1.1 and modify the new `auth.conf` file to include any customizations from your legacy file. To do this you can pass in an answer file with `q_migrate_auth_conf_after_upgrade=y`

### Notable changes and additions made to legacy `auth.conf` since 3.8.4

Here are some notable changes that have been made to the endpoints in `auth.conf` between the 3.8.x and PE 2016.1.1 versions:

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

The 2015.2 series also added in the status route at `/puppet/v3/status`.

In 2016.1.1, two new endpoints were added to the new `auth.conf`:

- `/puppet/v3/environment_classes`
- `/puppet/v3/static_file_content`

These new endpoints are not compatible with legacy `auth.conf` files. 

### Notable changes and additions in `auth.conf`

Refer to the Puppet Server documentation for more information about the [new `auth.conf` file format]({{puppetserver}}/config_file_auth.html). 

## Hard tabs for indentation in Hiera YAML files cause errors after upgrading

If you're upgrading to the latest version of PE, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading. 

## Customized PE initialization script defaults are not replaced on upgrade

If you customized the `pe-console-services`, `pe-puppetserver`, or `pe-puppetdb` initialization scripts in `/etc/sysconfigs` (on RHEL-based systems) or `/etc/defaults` (on Debian-based systems) the customizations will be lost when you upgrade. This is mainly due to major path changes between versions. However, please note that the customized scripts will be saved in the same location with a `.bak` extension.

## Before upgrading, correct invalid entries in `autosign.conf`

The upgrade will not configure the PE console if any entries in `/etc/puppetlabs/puppet/autosign.conf` don't conform to the [autosign requirements]({{puppet}}/ssl_autosign.html#the-autosignconf-file). Please correct any invalid entries before upgrading PE.

## Upgrade will revoke all authentication tokens

The upgrade will automatically revoke all existing authentication tokens, regardless of their lifetime. After the upgrade is complete, all PE users will need to [generate a new authentication token](./rbac_token_auth.html#generating-a-token) in order to access PE. 
