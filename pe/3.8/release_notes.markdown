---
layout: default
title: "PE 3.8 » Release Notes"
subtitle: "Puppet Enterprise 3.8 Release Notes"
canonical: "/pe/latest/release_notes.html"
---

[environments]: /puppet/3.8/reference/environments.html

This page describes new features and general improvements in the latest Puppet Enterprise (PE) release.

## New Features and Improvements in PE 3.8.7

PE 3.8.7 is a release to provide security improvements. For more information, review the [security and vulnerability announcements](/security), as well as the [known issues](./release_notes_known_issues.html).

## New Features and Improvements in PE 3.8.6

PE 3.8.6 is a release to provide security improvements. For more information, review the [security and vulnerability announcements](/security), as well as the [known issues](./release_notes_known_issues.html).

## New Features and Improvements in PE 3.8.5

PE 3.8.5 is a release to fix bugs and provide security improvements. For more information, review [resolved issues documentation](./release_notes_security.html), as well as the [known issues](./release_notes_known_issues.html).

### Deprecations in PE 3.8.5

Debian 6, Fedora 21, and Ubuntu 15.04 have been removed as Puppet agent platforms.

## New Features and Improvements in PE 3.8.4

PE 3.8.4 is a release to fix bugs and provide security improvements. For more information, review [the release notes security documentation](./release_notes_security.html).

### Upgrade Note: PuppetDB `statement_cache_size` Now Managed By puppet_enterprise Module

If you're upgrading to 3.8.6, please note that we are now managing PuppetDB's [statement_cache_size](/puppetdb/2.3/configure.html#statements-cache-size) via the puppet_enterprise module. If you are managing the configuration of that setting via Puppet you may run into duplicate resource errors or the value being overriden by PE. 

To manage this setting via the puppet_enterprise module, add the following to your hiera configuration: 

~~~
puppet_enterprise::puppetdb::database_ini:: statements_cache_size:<VALUE>
~~~

## New Features and Improvements in PE 3.8.3

### MCollective `plugin.activemq.randomize` Can Now Be Toggled

The [`plugin.activemq.randomize`](https://docs.puppetlabs.com/mcollective/reference/plugins/connector_activemq.html#pluginactivemqrandomize), which helps configure MCollective can now be toggled to `true`. By default, it is set to `false`.

## New Features and Improvements in PE 3.8.2

PE 3.8.2 is primarily a security and bug fix release. It contains updated versions of Apache HTTP Server, Ruby Rack and Rails, and Java, as well as other improvements described in the [release notes documentation](./release_notes_security.html).

## New Features and Improvements in PE 3.8.1

### Improvement to the PE Classification Data Migration Tool For PE 3.3 to PE 3.8 Upgrades

In PE 3.8.0, the classification data migration tool returned an error during the convert step if you had classification data applied directly to a node and through one or more node groups.

The classification data migration tool now automatically resolves the conflict for nodes that have classification data applied directly as well as through a **single node group**. See the [documentation on resolving conflicts](./install_upgrade_migration_tool_conflicts.html) for more information.


### AIX Agent Stack Rebased to Ruby 2.1.6

See [this description of the changes](./release_notes_security.html#mcollective-core-was-dumping-on-aix) for more information.

### `max-requests-per-instance`

This feature, now added to Puppet Server, enables you to set the maximum number of requests per instance of a JRuby interpreter before it is killed. When it is killed, all of its memory is reclaimed and is then replaced in the pool with a fresh new JRuby interpreter. This helps to mitigate memory leak issues in Puppet code and to generally keep the server up by avoiding a situation where any one JRuby interpreter consumes too much ram.

## New Features in PE 3.8.0

### Classification Data Migration Tool for Upgrades From PE 3.3

PE 3.8 comes with a migration tool to help you migrate PE 3.3 classification data to PE 3.8.

In PE 3.7, we introduced a new node classifier, which allowed for greater automation but wasn't compatible with some of the ways PE 3.3 classified nodes. The migration tool helps you migrate PE 3.3 nodes and classification to the new node classifier, and provides guidance on fixing classifications that aren't compatible with PE 3.8.

For details about the changes to node classification and important instructions on how to migrate your classification data to PE 3.8, see [the migration tool documentation](./install_upgrade_migration_tool.html).

### r10k Code Management Tool and Quick Start Guide

This release adds [r10k](./r10k.html), a code management tool that lets you use your Git repositories to manage the configuration and contents of your Puppet [environments][] (such as production, development, or testing).

Read [the r10k quick start guide](./quick_start_r10k.html) to get started. In this walkthrough, you'll use the code you wrote for the [Hello, World! QSG](./quick_start_helloworld.html) to learn how r10k can help you deploy different versions of code across different environments.

### Razor Now Fully Supported

[razor]: ./razor_intro.html

[Razor][], Puppet Labs’ tool for provisioning bare metal servers, has moved from tech preview to a fully supported solution. With Razor's policy-based approach, you can automatically discover bare-metal hardware, dynamically configure operating systems and/or hypervisors, and hand nodes off to PE for workload configuration. What used to take hours of manual work now takes minutes. See the [Razor documentation][razor] to learn more.

### Puppet Agent on Network Devices

Thanks to a number of partnerships, Puppet Enterprise has been adding support for running Puppet agent on network devices. Currently we support the [Cumulus Linux](./install_cumulus.html) and the [Arista EOS](./install_eos.html) platforms.

### Puppet 4 Language Parser

**The Puppet 3.x language parser is deprecated.**

The Puppet 4 language parser (i.e., the future parser) gives you valuable language features, makes debugging easier, and will help keep your Puppet code compatible with future releases in the next major series. During new installations, you'll be asked if you want to turn on the new  language parser---this is recommended for all **NEW** Puppet users.

If you'll be using Puppet code you did **NOT** create with the Puppet 4 language parser, **DO NOT** enable this feature. Instead, we recommend you use the [Puppetlabs catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview), which enables you to use the Puppet 4 parser in a test environment to ensure it works with your existing Puppet code.

Before upgrading to the next major version of PE, you will need to ensure your Puppet code works with the new parser. See the [Puppet 4 language parser docs](http://links.puppetlabs.com/future_parser) for instructions on enabling the new parser either globally or in a per environment setting.

**Note**: PE 3.8.0 users should see [Workaround to Run the Puppet 4 Language Parser in Non-Production Environments](./release_notes_known_issues.html#workaround-to-run-the-puppet-4-language-parser-in-nonproduction-environments)

## Improvements in PE 3.8.0

### PE 3.8 Tagmail Users Should Use the puppetlabs-tagmail Module

If you want to use tagmail in PE 3.8, you need the [puppetlabs-tagmail module](https://forge.puppetlabs.com/puppetlabs/tagmail), available from the Puppet Forge. Puppet 3.8 still includes `tagmail.conf`, but PE users should refer to the module, as the built-in tagmail feature will be completely removed in a future release.

### String Interpolation in the PE Console

In prior PE versions, you could only enter literal values for parameters and variables in the console.

Now, when entering string values for parameters and variables, you can interpolate fact values and limited expressions. This makes it possible to use values like `"I live at $ipaddress"`, which interpolates the value of the `$ipaddress` fact, as well as values such as `${$os["release"]["full"]}`, which interpolates the result of the embedded expression.

For more information on the syntax and restrictions for string interpolation in the console, see [Tips on specifying parameter and variable values](./console_classes_groups.html#setting-class-parameters).

### Upgrades for Large Environment Installations

Upgrading your large environment installation (LEI) involves a combination of steps that you must perform across your core Puppet Enterprise components, your compile masters, and your ActiveMQ hubs and spokes. The [LEI upgrade doc](./install_lei_upgrade.html) details the steps you’ll perform to upgrade your LEI from PE 3.7.2 to 3.8.0.

### Deleting a User From RBAC

There is a new endpoint in the Role-Based Access Control API that lets you delete a local or remote user from PE. For more information, see the [API documentation](./rbac_users.html#delete-userssid).

### Deleting a User Group From RBAC

There is a new endpoint in the Role-Based Access Control API that lets you delete a local or remote user group from PE. For more information, see the [API documentation](./rbac_usergroups.html#delete-groupssid).

### Verifying Certificates for External LDAP Directories

When configuring the RBAC service to connect to an external directory server, you can now verify an SSL certificate for the server. For more information, see the [RBAC documentation](./rbac_ldap.html#verify-directory-server-certificates).

### New RBAC Permission to Edit Parameters and Variables

This permission allows a user in the given user role to tune classification by editing parameters and variables in a class, without giving the user permission to add or delete classes.

## Deprecations in PE 3.8.0

### Cloud Provisioner is Deprecated

Cloud Provisioner is deprecated in this release and will eventually be removed from Puppet Enterprise. For this reason, Cloud Provisioner is no longer installed by default. If you have been using Cloud Provisioner in your existing PE infrastructure and would like to continue using it, you can install it separately, as described in the [Installing section of Cloud Provisioner documentation](./cloudprovisioner_configuring.html#installing).

Instead of Cloud Provisioner, we recommend using the [AWS Supported Module](https://forge.puppetlabs.com/puppetlabs/aws) going forward.

### Live Management is Deprecated

Live Management is deprecated in PE 3.8.0 and will be replaced by improved resource management functionality in future releases. For this reason, Live Management is no longer enabled by default. If you have been using Live Management in your existing PE infrastructure and would like to continue using it with PE 3.8, see the instructions for [enabling Live Management](./console_navigating_live_mgmt.html#disablingenabling-live-management).

### Some Platforms are Deprecated for PE Infrastructure Components

Several Puppet master platforms have been deprecated in PE 3.8 and will be removed in future versions of PE. *PE agent support on these platforms will continue*. The deprecated Puppet master platforms include all 32-bit master platforms, all Debian versions, EL 5 versions, and Ubuntu 10.04. Going forward, only 64-bit Puppet masters will be supported, but 32-bit agent support will continue to be offered. The complete list is as follows:

* centos-5-i386
* centos-5-x86_64
* centos-6-i386
* debian-6-i386
* debian-6-x86_64
* debian-7-i386
* debian-7-x86_64
* oracle-5-i386
* oracle-5-x86_64
* oracle-6-i386
* redhat-5-i386
* redhat-5-x86_64
* redhat-6-i386
* scientific-5-i386
* scientific-6-i386
* sles-11-i386
* ubuntu-1004-i386
* ubuntu-1004-x86_64
* ubuntu-1204-i386
* ubuntu-1404-i386


See the [system requirements](./install_system_requirements.html) for a list of supported platforms.
