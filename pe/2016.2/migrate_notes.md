---
layout: default
title: "Migrating Puppet Enterprise: Important notes"
canonical: "/pe/latest/migrate_notes.html"
---

>**Note**: A complete list of known issues is provided in the [PE release notes](./release_notes_known_issues.html). Please review this list before upgrading.


## Migrating to PE 2016.2 from 3.8?

The following changes were made in PE last year. If you're migrating from 3.8, please review these items before migrating.

### Static catalogs enabled by default

A *static* catalog includes metadata that specifies the desired state of any [file resources]({{puppet}}/types/file.html) on a node that has `source` attributes pointing to `puppet:///` locations. This metadata can refer to a specific version of the file, rather than the latest version, and can confirm that the agent is applying the appropriate version of the file resource for the catalog. Also, because the metadata is provided in the catalog rather than calculated by the agent, Puppet runs on the agent finish sooner.

In PE, static catalogs are disabled across all environments for new installations. To use static catalogs in PE, **you must** [enable file sync](./cmgmt_filesync.html#enabling-or-disabling-file-sync). See [Static Catalogs in Puppet Enterprise](./static_catalogs.html) for more information.


### Major changes to configuration files and directories

Last year in PE, we changed the locations of several important configuration files and directories. For a summary of the changes, see [major path changes in PE](./install_upgrading_path_changes.html).

### Important notes about PE databases 

- PE 2015.2 eliminated use of the console database (`pe-console`). 

### New Puppet language parser

PE 2015.2 included major revisions to the Puppet language and, consequently, the language parser (introduced for testing in PE 3.8). Before upgrading, ensure your Puppet code is compatible with the new parser (previously referred to as the "future parser").

If you're upgrading from PE 3.8, you can check your Puppet code against the new parser using the [Puppet catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview). This module allows you to use the Puppet 4 parser in a test environment to ensure it works with your existing Puppet code.

* The `enable_future_parser` parameter is deprecated. If you enabled the Puppet 4 language parser in PE 3.8.x via the console, Hiera, or a third-party classification tool, **after migrating** you must [use the console to remove](./console_classes_groups_making_changes.html#deleting-parameters) the paramater from the `puppet_enterprise::profile::master` class. 
* The `parser=future` setting is no longer valid. If you used this setting in any `environment.conf` files to enable the parser in PE 3.8.x, you'll see warning messages during your upgrade. After you upgrade, remove this setting from any `environment.conf` files.

### Nginx replaced Apache as the Puppet Enterprise web server

In PE 2015.2, nginx replaced Apache as the PE webserver. This change was made to enhance security and performance. This change should be mostly transparent to users, except for the following:

- The webserver user changes from `pe-apache` (`pe-httpd`) to `pe-webserver`.
- The webserver service changes from `pe-httpd` to `pe-nginx`.

## Changes to `auth.conf` when migrating from 3.8

For versions of PE previous to 2016.2, the [`auth.conf`]({{puppet}}/config_file_auth.html) file managed access to Puppet's HTTPS API. This file was located at `/etc/puppetlabs/puppet/auth.conf` on the Puppet master.

PE 2015.3 introduced a new `auth.conf` (`/etc/puppetlabs/puppetserver/conf.d/auth.conf`) that makes use of Puppet Server's trapper-keeper services.  

We recommend that you address the changes, as some functionality (e.g., console services) may not be available after upgrade if the endpoints aren't authorized.

Refer to [Puppet Server Configuration Files: Migrating to the HOCON auth.conf Format]({{puppetserver}}/config_file_auth_migration.html) for instructions on migrating to the new format.

### Notable changes and additions made to legacy `auth.conf` since 3.8.x

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

In 2016.1.1, two endpoints were added to the new auth.conf:

- `/puppet/v3/environment_classes`
- `/puppet/v3/static_file_content`

These new endpoints are not compatible with legacy auth.conf files.

### Notable changes and additions in `auth.conf`

Refer to the Puppet Server documentation for more information about the new `auth.conf` file format. 

## Hard tabs for indentation in Hiera YAML files cause errors after upgrading

If you're migrating to the PE 2016.2 series, ensure that any Hiera YAML files do not include hard tabs for indentation. Hard tabs in these files will cause errors after upgrading. 
