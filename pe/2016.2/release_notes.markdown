---
layout: default
title: "New features, enhancements, and deprecations"
canonical: "/pe/latest/release_notes.html"
---

[tech preview]: https://puppetlabs.com/services/tech-preview

This page describes new features, enhancements, and deprecations in this Puppet Enterprise (PE) release.

For more information about this release, see:

* [Known issues](./release_notes_known_issues.html)
* [Resolved issues](./release_notes_resolved_issues.html)
* [Security and vulnerability announcements](/security)

## New features in PE 2016.2.1

PE 2016.2.1 is primarily a release to fix bugs and provide security improvements. For more information, review the [resolved issues](./release_notes_resolved_issues.html) and [security vulnerabilities](/security) documentation.

### Platform enhancements

This release adds the Puppet agent on the following platforms:

- RHEL 6 and 7 (s390x)
- SUSE Linux Enterprise Server (SLES) 11 and 12 (s390x)
- Fedora 24 (x86_64, i386)

### Puppet Application Orchestration enhancements

The Puppet Forge has released the [Puppet wordpress_app](https://forge.puppet.com/puppetlabs/wordpress_app/readme). This module demonstrates an example application model and contains application components you can use to set up a WordPress database, a PHP application server, and an HAProxy load balancer. With these components, you can build two WordPress applications: a simple LAMP stack or a complex stack that uses load-balancing.

### Related release notes

#### Puppet release notes

This version of PE includes Puppet version 4.5.3. Refer to the [Puppet release notes]({{puppet}}/release_notes.html) for more information.

#### Puppet agent release notes

This version of PE includes Puppet agent version 1.5.3. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html#puppet-agent-153) for more information.

#### PuppetDB release notes

This version of PE includes PuppetDB version 4.1.4. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

#### Puppet Server release notes

This version of PE includes Puppet Server version 2.4. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.

## New features in PE 2016.2

### Puppet orchestrator API

The [Puppet orchestrator API](./orchestrator_api_endpoints.html) is now available. It consists of endpoints that allow you to start and stop jobs and analyze events that occur during those jobs.

### Improved installation and upgrading

We've made significant improvements to the installation and upgrade process, particularly for [text-mode installations and upgrades](./install_text_mode.html). This release also introduces [the `pe.conf` file](./install_text_mode.html#the-peconf-file), which, in future releases, is used to manage and maintain classification of your PE infrastructure components from one release to the next.

## Enhancements in PE 2016.2


### Platform enhancements

* The default location for `hiera.yaml` has moved to `/etc/puppetlabs/puppet/hiera.yaml` (from `/etc/puppetlabs/code/hiera.yaml`).

  On upgrades, the existing `hiera.yaml` file is automatically moved to the new location, **unless** you have modified the file. If you have modified the `hiera.yaml` file, move it to the new location. Do not leave a copy of `hiera.yaml` in the code directory. Future upgrades fail if `hiera.yaml` is detected in the old location. <!--PE-13367--> <!--HI-490-->

* PE's PostgreSQL implementation now relies on the agent certificate for authentication. Previously, it used password authentication.

* Ubuntu 16.04 (Xenial Xerus) added as a supported OS for both agent and Puppet master roles.

* When creating new modules, the `puppet module generate` command writes an `examples` directory instead of the deprecated `tests` directory.

### Console enhancements

In addition to a refreshed appearance, this version adds these enhancements to the PE console:

* The [Overview page's redesigned leaderboard](./CM_overview.html#using-the-overview-page) shows more granular node status information after each Puppet run. The leaderboard lets you filter and view nodes by run status, including nodes run in enforcement mode, nodes with no-op resources, and nodes for which a cached catalog was used due to failure of the intended catalog.

* An improved timeframe selector on the Events page lets you view events from the last run only, or from the past five minutes, 30 minutes, 60 minutes, or 24 hours.

* [Fact filters](./CM_overview.html#filter-using-facts) on the Overview and Reports pages now use operator characters (**~**) rather than words ("matches regex").

* The node graph now allows filtering of resources by run status.

* When filtering resources by tag in the node graph, filter results are displayed in the details pane as a clickable list.

* Two new [Nodes role permissions](./rbac_permissions.html#display-names-and-corresponding-system-names) provide control over which users can view and edit node data from PuppetDB. You can access the new `nodes:view_data` and `nodes:edit_data` role permissions through the [RBAC API](./rbac_permissionsref_v1.html).

### Node Management enhancements

This version includes these enhancements to Node Management:

* You can now use structured and trusted facts in the console to create node group rules. Previously, structured and trusted facts were available only through the classifier API. See [Adding nodes dynamically](./console_classes_groups.html#adding-nodes-dynamically) for details.

* The [`group-children` endpoint](./nc_groups_children.html) retrieves a specified group and its descendents, so you can view hierarchy within a specific node group with a single query.

* The [`unpin-from-all`](./nc_commands.html) commands endpoint is no longer a tech preview feature. The endpoint is fully supported.

### Code Manager enhancements

* This release adds a Code Deployers role in role-based access control (RBAC). This role's default permissions are limited to deploying code and managing token lifetime.

  If you are already using Code Manager with a deployment role that you created, you do not need to change to the new Code Deployers role. <!--PE-12868-->

* Code Manager and r10k now support Git access through proxy servers. This allows you to configure proxies for specific Git sources and modules accessed via HTTP or to set a global proxy configuration.

  HTTP Basic and Digest authentication are supported. Note that this is for authentication to the proxy server itself, not for the service being contacted. See [Code Manager](./code_mgr_custom.html) or [r10k](./r10k_custom.html) documentation for proxy setting information. <!--CODEMGMT-678-->

* This release includes a [Code Manager troubleshooting guide][./code_mgr_troubleshoot], with solutions for some common issues and an advanced troubleshooting walk-through.

* When Code Manager and file sync are enabled, ownership of all files in the code directory is changed to pe-puppet. This eliminates the need for manually changing the ownership and ensures that when enabled, Code Manager and file sync can make changes as needed. <!--PE-14232-->

* The `puppet-code` command uses the PE CA certificate path `/etc/puppetlabs/puppet/ssl/certs/ca.pem` by default. Previously, this command defaulted to `/etc/puppetlabs/client-tools/ssl/certs/ca.pem`.

### PE client tools enhancements

* On installing the PE client tools package, a new global configuration file for `puppet-access` is created at `~/.puppetlabs/client-tools/puppet-access.conf` on PE-managed machines. This file removes the need to correctly specify a `--service-url` setting before using `puppet-access` to generate authentication tokens.

### Razor enhancements

* Custom Razor configurations are moved from the `config.yaml` file to class parameters within the `pe_razor` module. Anything you specified in `config.yaml` you must now specify in class parameters. This change enables easier upgrades in the future. However, you must manually migrate your custom Razor configurations from `config.yaml` to class parameters when you upgrade to PE 2016.2.

  **Important**: The `protect_new_nodes` parameter is the most critical migrated setting. To prevent accidentally overwriting machines during upgrade, the default for `protect_new_nodes` was changed to `true` in PE 2016.2 and later. If your environment and workflows rely on provisioning all new nodes, you must manually change `protect_new_nodes` to `false` after upgrading. See [Provisioning a node](./razor_using.html) for recommended provisioning workflows, including managing the `protect_new_nodes` setting.

* The [`razor config` command](razor_client_commands.html#config) and [`config` endpoint](razor_reference.html#view-configuration-config) display details about your Razor configuration, including most of the class parameters of the `pe_razor` module.

* The `enable_smb_share` class parameter of the `pe_razor` module [configures SMB share](./razor_windows_install.html#configure-smb-share). If you enable this parameter (`true`), Razor installs Samba and maps to the default repository storage location, providing a simplified workflow for installing Windows nodes. If you change `enable_smb_share` from `true` to `false`, the share remains enabled but isn't managed by Puppet.

## Related release notes for this version of PE

#### Puppet release notes

This version of PE includes Puppet version 4.5.2. Refer to the [Puppet release notes]({{puppet}}/release_notes.html) for more information.

#### Puppet agent release notes

This version of PE includes Puppet agent version 1.5.3. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html#puppet-agent-152) for more information.

#### PuppetDB release notes

This version of PE includes PuppetDB version 4.1.4. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

#### Puppet Server release notes

This version of PE includes Puppet Server version 2.4. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.





