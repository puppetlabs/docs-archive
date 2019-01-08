---
layout: default
title: "PE 2015.2 » Release Notes"
subtitle: "Puppet Enterprise 2015.2 Release Notes"
canonical: "/pe/latest/release_notes.html"
---

[environments]: /puppet/4.2/reference/environments.html

This page describes new features and general improvements in the latest Puppet Enterprise (PE) release.

For more information about this release, see [Known Issues](./release_notes_known_issues.html) and [Security and Bug Fixes](./release_notes_security.html).

## New Features and Improvements in PE 2015.2.3 (November 5, 2015)

For a full list of bug and security fixes, review the [security and bug fix documentation](./release_notes_security.html). 

### New Agent Platform Support

This PE release includes support for the following agent platforms:

- Red Hat Enterprise Linux 4 (x86\_64)
- AIX 5.3, 6.1, and 7.1 (Power)
- Solaris 11 (i386 and SPARC)


## New Features and Improvements in PE 2015.2.2 (October 13, 2015) 

PE 2015.2.2 is release to fix several critical bugs. For a list of these bug fixes, review the [release notes documentation](./release_notes_security.html). 

## New Features and Improvements in PE 2015.2.1

For a full list of bug fixes, review the [release notes documentation](./release_notes_security.html). 

### New Agent Platform Support

This PE release includes support for the following agent platforms:

- Mac OS X 10.9 (Mavericks) (x86\_64)
- Mac OS X 10.10 (Yosemite) (x86\_64)
- Debian 8 (Jessie) (i386, amd64)
- SLES 10 (i386, x86_64)
- Solaris 10 (i386, SPARC)
- Ubuntu 15.04 (i386, amd64)

### New Postrun Command in `pe_r10k` Class

The `pe_r10k` class can now include a postrun command, an optional command run after r10k finishes deploying environments. This allows you to trigger automatic refreshes after deployment.

## New Features in PE 2015.2

### UI Redesign

Puppet Enterprise's user interface (UI) has had a significant redesign in this release. The main navigation in the PE user interface has moved to the left side of the screen to maximize the work area and support your workflows. The new design also supports future enhancements and new features.

The main navigation is now split into two main workspaces: Node Manager, where you classify and manage your nodes, and Configuration Manager, which is described below.

### Configuration Manager

[Configuration Manager](./CM_overview.html) leverages PE data to provide information on node states after Puppet runs, as well as events related to classes, nodes, and resources. Configuration Manager also provides reports and logs that enable you to audit your infrastructure and determine the cause of errors. The **node graph** is a graphic representation of a node's resources, enabling you to plan your configurations and more easily track the source of problems. The features in Configuration Manager replace Event Inspector and Reports, which you might have used in previous versions of PE.

### Major Configuration File and Directory Changes

In PE 2015.2 (and Puppet 4.2), we changed the locations of several important configuration files and directories. We also changed Puppet's packaging to install different things in different places.

[Major Path Changes in PE 2015.2](./install_upgrading_path_changes.html) is a summary to quickly orient users arriving from PE 3.8.1. This includes information about Hiera path changes.

### New All-in-One `puppet-agent` Package

For agents running on *nix systems, you'll now install an all-in-one (AIO) `puppet-agent` package, which includes private versions of tools like Facter and Hiera. It also includes cfacter and MCollective, Ruby 2.1.6, OpenSSL 1.0.0s, and our gem dependencies.

The `puppet-agent` package installs into its own area in `/opt/puppetlabs/`.

Puppet masters running PE 2015.2 will manage agents running 3.8.x, but we recommend you upgrade to take advantage of the most recent features in the Puppet agent. To upgrade to PE 2015.2.1 agents **must** first be on PE 3.8.2.

Please note that the [agent upgrade instructions](./install_upgrading_agents.html) have changed. To upgrade your Puppet agents you'll need to download and install the puppetlabs-puppet\_agent module. We created this module to handle some of the heavy-lifting in the move to the "AIO" Puppet agent. This module automates important path and configuration changes to make the upgrade process much easier.

([Agent installation instructions](./install_agents.html) have stayed the same.)

On Windows, you'll use the same type of MSI package as before, only now it will have a different name when you download it.

### New Puppet Language Parser

PE 2015.2 includes major revisions to the Puppet language and, consequently, the language parser (introduced for testing in PE 3.8). The rewritten parser includes new capabilities like iteration and type-checking for variables, as well as deprecations and changed behaviour. Most notably, the stricter, more predictable parsing of numbers, empty strings, and “undef”/”nil” comparisons may cause changes to a node’s catalog without necessarily producing an error.

Before upgrading, please review the [upgrade notes about the new language parser](./install_upgrading_notes.html#new-puppet-language-parser) and ensure your Puppet code is compatible with the new parser.

### Puppet Enterprise catalog_preview Module

The catalog\_preview module helps you [prepare for migration/upgrade from Puppet 3.8.1 to Puppet Enterprise 2015.2](./migrate_pe_catalog_preview.html). The module compiles and compares two catalogs: one in a baseline environment using the current Puppet 3 language parser, and another in a preview environment using the “future” parser to validate your syntax against the new Puppet 4 language. The output from this comparison highlights issues you might experience when you migrate/upgrade from Puppet Enterprise 3.8.1 to Puppet Enterprise 2015.2.

### Configure r10k Using a Module

PE 2015.2 includes the `pe_r10k` module, which allows you to configure r10k in the PE console instead of manually managing r10k’s configuration file. We've deleted the outdated r10k manual configuration docs and added new documentation for configuring r10k with an [answer file](./r10k_config_answers.html) or with [the PE console](./r10k_config_console). See the [r10k documentation for more information](./r10k_install_prep.html#upgrading-from-earlier-versions-of-r10k).

### Nginx Replaces Apache as the Puppet Enterprise Webserver

In PE 2015.2, nginx replaces Apache as the PE webserver. This change was made to enhance security and performance value for the changes in this and future release of PE. This change should be mostly transparent to users, save for the following:

- The webserver user changes from `pe-apache` (`pe-httpd`) to `pe-webserver`.
- The webserver service changes from `pe-httpd` to `pe-nginx`.

### Support for Puppet Agents on the Cisco NS-OX Operating System for Network Switches

Puppet Enterprise supports the Cisco NX-OS operating system as a platform for running Puppet agents on their network switches. Refer to the [Installing Cisco NS-OX Agents](./install_nxos.html) and the [puppetlabs-ciscopuppet module](https://forge.puppetlabs.com/puppetlabs/ciscopuppet) for more information.

### Connect to PuppetDB Using SSL

You can connect to PuppetDB using SSL. See the [PuppetDB configuration documentation](./config_puppetdb.html#connect-to-puppetdb-using-ssl) for more information.

## Improvements to PE 2015.2

### Upgrade Notes and Warnings

Before upgrading, please review [Upgrading Puppet Enterprise: Notes and Warnings](install_upgrading_notes.html), which includes important information about PostgreSQL upgrades and the new Puppet language parser, as well as other changes in this version of PE.

### Connect to an External Directory Using a StartTLS Connection

The Role-Based Access Control (RBAC) service now includes the option to use a StartTLS connection to connect to an external directory service. Currently, this setting can only be specified [through the RBAC API](./rbac_dsref.html#put-ds).

### `hiera.yaml` Default Path Change

In previous versions of PE, the default path for `hiera.yaml` was `/etc/puppetlabs/puppet/hiera.yaml`. PE 2015.2 moves the default path to `/etc/puppetlabs/code/hiera.yaml`.

In addition, `hiera.yaml` is automatically configured for use after installing or upgrading.

### Access Permission For Accepting/Rejecting Certificate Signing Requests

In PE 2015.2, the only remaining functionality of the **Console page - Write** permission is to accept and reject certificate requests. To better reflect the actual functionality of this permission, the **Console page - Write** permission has been renamed to **Certificate requests - Accept and reject**.

When upgrading, users who had the **Console page - Write** permission in earlier versions will automatically be granted the **Certificate requests - Accept and reject** permission.

To learn more about permissions and Role Based Access Control (RBAC), see [About RBAC Permissions](./rbac_permissions.html).

### The **Default** Node Group is Now Called **All Nodes**

In previous versions of PE, there was a node group named **default**. In PE, this node group is at the very top of the node group hierarchy structure. All Puppet-managed nodes are members of this group by default. To make the name more intuitive for users, the name of the **default** group has been changed to **All Nodes** in PE2015.2.

### New Status Endpoints to Monitor the Health of PE services

There is a new Status API to monitor the health of the Activity, RBAC, and Node Classifier services. To learn more, see the [Status API documentation](./status_api.html).

>**Note**: The Status API is included in Puppet Enterprise 2015.2 as a tech preview. Puppet Labs tech previews provide early access to new technology still under development. As such, you should only use them for evaluation purposes and not in production environments. You can find more information on tech previews on the [tech preview](http://puppetlabs.com/services/tech-preview) support scope page.

### Purging a Node

There is a new `puppet node purge <NODE NAME>` command which deactivates the node from PuppetDB, deletes the Puppet master’s information cache for the node, and frees up the license that the node was using. For more details, see the [Purging Nodes documentation](./console_classes_groups_making_changes.html#purging-nodes).

## Deprecations and Removed Features in Puppet Enterprise 2015.2

### Cloud Provisioner and Live Management Are No Longer Part of Puppet Enterprise

Both Cloud Provisioner and Live Management were deprecated in PE 3.8.x and have been fully removed in PE 2015.2.

If you want information on using Live Management in a previous release, see the [Live Management documentation in PE 3.8](/pe/3.8/console_navigating_live_mgmt.html).

If you want information on using Cloud Provisioner in a previous release, see [Cloud Provisioner documentation in PE 3.8](/pe/3.8/cloudprovisioner_overview.html).

### Some Platforms No Longer Available as Puppet Masters

Some Puppet master platforms were deprecated in PE 3.8.x and have been fully removed in PE 2015.2. These include:

- All 32-bit master platforms.
- All Debian versions.
- EL 5 versions.
- Ubuntu 10.04.

### Console Rake API Removed

The console rake API is no longer available in PE. It was deprecated in PE 3.7 and officially removed in PE 2015.2. It has been replaced with the [Node Classifier Service API](./nc_index.html), the [RBAC Service API](./rbac_serviceindex.html), and the [Activity Service API](./rbac_activityapis.html).

If you're on an older version of PE and still want to use the Rake API, see [the Rake API docs for PE3.8](/pe/3.8/console_rake_api.html).

### Windows 2003 deprecated

As of this release, support for Windows 2003 is deprecated. It will be discontinued in a future release.

### Console Database Removed

PE 2015.2 eliminates the console database (`pe-console`). During upgrades of PE-managed PostrgreSQL databases, we remove the console database. However, if you're using an external PostgreSQL instance, we will not remove the console database. You can remove this database or leave it in place. It will not affect your system if left in place.

