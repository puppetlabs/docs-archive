---
layout: default
title: "New features, enhancements, and deprecations"
canonical: "/pe/latest/release_notes.html"
---

[tech preview]: https://puppetlabs.com/services/tech-preview

This page describes new features, enhancements, and deprecations in the latest Puppet Enterprise (PE) release.

For more information about this release, see:

* [Known issues](./release_notes_known_issues.html)
* [Resolved issues](./release_notes_resolved_issues.html)
* [Security and vulnerability announcements](/security)

> **Tip:** This PE release contains several components that have additional release notes. Refer to [Related release notes](./release_notes_resolved_issues.html#related-release-notes-for-this-version-of-pe) for more information.

## New features in PE 2016.1.2

PE 2016.1.2 is primarily a release to fix bugs and provide security improvements. For more information, review the [resolved issues](./release_notes_resolved_issues.html) and [security vulnerabilities](/security) documentation.

### Platform enhancements

This release adds the following Puppet agent platforms:


* Ubuntu 16.04
* Huawei CloudEngine network switches

   Puppet and Puppet Enterprise support HuaweiOS 6.0 and higher for running Puppet agents on some models of Huawei network switches. Supported switches include:

   * Huawei CloudEngine 12800 series switches (CE12804/CE12808/CE12812/CE12816)
   * Huawei CloudEngine 8800 and 7900 series switches (CE8860/CE7850)
   * Huawei CloudEngine 6800 series switches (CE6850/CE6850HI)


## Deprecations in PE 2016.1.2

Debian 6 and Ubuntu 15.04 and 15.10 have been removed as Puppet agent platforms. See the known issues for instructions on [removing these deprecated pe_repo classes after upgrade](./release_notes_known_issues_install.html#new-remove-deprecated-perepo-classes-after-upgrade).

## New features in PE 2016.1.1

**Note**: This new release is PE 2016.1.1 and contains all of the new features listed below, as well as a connection pool fix to PuppetDB for an issue that was found before PE 2016.1.0 shipped. PE 2016.1.0 was never released to the public. 

### Puppet Labs is now Puppet

Our company name has changed! Our docs site has a new look and we have changed our site URL to docs.puppet.com. But not everything is changing. If you have bookmarked pages from past releases, you will be redirected to those pages in our new URL. In addition, the following **will not** change at this time, but might change in the future. We will keep you up-to-date on any changes coming your way.

* Configuration directories such as `/etc/puppetlabs/puppet/`
* GitHub repos under https://github.com/puppetlabs
* Supported Forge modules with `puppetlabs` in the module name and commands, such as `puppet module install puppetlabs-aws`
* URLs for package repositories such as those needed for Puppet updates: apt.puppetlabs.com or yum.puppetlabs.com
* Non-root paths: `~/.puppetlabs/etc/puppet/puppet.conf`
* Java/Cloujure namespaces: `puppetlabs.puppetdb.core`

### Static catalogs

A *static* catalog includes metadata that specifies the desired state of any [file resources]({{puppet}}/types/file.html) on a node that has `source` attributes pointing to `puppet:///` locations. This metadata can refer to a specific version of the file, rather than the latest version, and can confirm that the agent is applying the appropriate version of the file resource for the catalog. Also, because the metadata is provided in the catalog, Puppet agents make fewer requests to the master.

In PE, static catalogs are disabled across all environments for new installations. To use static catalogs in PE, [enable file sync](./cmgmt_filesync.html#enabling-or-disabling-file-sync). See [Static Catalogs in Puppet Enterprise](./static_catalogs.html) for more information.

### `environment-class-cache-enabled` server setting

A new optional configuration setting for the Puppet server enables improved performance when updating classes. The [`environment-class-cache-enabled` setting](./config_puppetserver.html#setting-environment-class-cache-enabled) specifies whether cached data is used when updating classes in the console.

By default, the setting is enabled (`true`) only if you have Code Manager enabled. When enabled, Puppet server and the node classifier rely on file sync to refresh classes.

If you set `environment-class-cache-enabled` to `true` but don't use Code Manager and file sync, you must [manually delete the environment cache]({{puppetserver}}/admin-api/v1/environment-cache.html) for environments in which code was updated, so that the latest class information appears in the console. Alternatively, you can restart the `pe-puppetserver` service to see updates.

### `unpin-from-all` endpoint

A new command endpoint in the node classifier API enables you to unpin specified nodes from all groups they're pinned to. For example, you can use this command to remove all references to a deleted node. The command is currently in [tech preview][] status. For details about the `unpin-from-all` command, see [Node classifier API commands](./nc_commands.html).

### Token revocation

The new v2 role-based access control (RBAC) service API introduces the ability to revoke authentication tokens. Token revocation improves security by giving users and administrators immediate control over who has access to PE.

### Optional nested user group search

In previous releases, RBAC solely offered "nested" group search capabilities, which created performance issues for some users managing large numbers of user groups. This release gives users the ability to easily opt out of nested search through the [directory service endpoints](./rbac_dsref_v1.html#put-ds).

**Note**: Users creating a fresh installation of PE who wish to retain the old nested group search behavior must [explicitly configure RBAC](./rbac_dsref_v1.html#put-ds).

### Node graph resource dependencies view

The node graph now offers a visual map of resource dependency chains with detailed information about each affected resource. The node graph will also issue a warning message when a dependency cycle is detected. See the [node graph documentation](./CM_graph.html) for a walkthrough of these new capabilities.

### Code Manager command line interface

The `puppet-code` command allows you to trigger environment deployments with Code Manager from the command line. This command is packaged with [PE client tools](./install_pe_client_tools.html). For information about configuring and using the command, see the [`puppet-code` documentation](./code_mgr_cli.html).

### Puppet Server developer dashboard

A [tech preview][] of a metrics dashboard for developers visualizes the [Status API's advanced metrics endpoints](./status_api.html#metrics-endpoints) for Puppet Server, such as the state of its JRuby interpreter pool and the most used API endpoitns, functions, and resources. See the [Puppet Server metrics documentation](./puppet_server_metrics.html) for more information.

## Enhancements in PE 2016.1.1

### Console enhancements

This version includes these enhancements to the console:

* A new **Add Group** widget on the node classification page makes it easier to specify required options when you create node groups.
* A new **Activity** tab appears in the detailed view of each node, allowing you to view changes made to the node without using the Activity Service API.
* Individual users and user groups can be deleted in the console by using the **Remove** buttons on the **Users** and **User Groups** pages.
* The console now supports anonymous binding to external directory services, making the **Lookup user** and **Lookup password** fields optional.
* Performance improvements provide faster response times when loading the node inventory in larger deployments.
* If there are syntax errors in any of the manifest files, classes declared in that file don't appear in the console. Parsing errors are logged to  `var/log/puppetlabs/console-services/console-services.log`. Previously, syntax errors in the site manifest prevented update of classes in that environment.
* A redesigned **Log** tab in the **Reports** section provides color-coded warning and error messages, as well as sortable notices in a more easily digestable format.
* The **Resource events** tab in the **Reports** section offers robust new search and sorting capabilities.
* The new **Puppet services status check** enables visual monitoring of the operational state of key Puppet services from within the console.


### Node Management enhancements

* **Update a specific environment with the `update-classes` endpoint**

  In previous releases, the `update-classes` endpoint retrieved an updated list of environments and classes for all environments. In this release, you can specify an optional `environment` parameter, allowing for targeted updating and a faster response. For details about the `update-classes` endpoint, see [Updating node classifier classes](./nc_update_classes.html).


### PE client tools enhancements

* **Manage client global configuration files**

   If you’re running the Puppet Access, Puppet code, or Puppet orchestrator clients from a PE-managed machine, you can have PE manage their global configuration files with the `puppet_enterprise::profile::controller` class. This class manages global configuration files in `/etc/puppetlabs/client-tools/`. See [Global configuration file management](./install_pe_client_tools.html#global-configuration-file-management) for details.

* **Install PE client tools on a PE managed machine**

   You can install the PE client tools package on a Puppet agent node. The [PE client tools installation documentation](./install_pe_client_tools.html#installing-pe-client-tools) has instructions.


### Puppet orchestrator enhancements

* **Preview jobs with `puppet job plan` command**

  The [`puppet job plan` command](./orchestrator_job_plan.html) prints the plan that would be generated if you ran a Puppet job. The job plan shows application instances and the node run order that would be included in a job run with the same options. The job plan lets you see what the orchestrator would do, without the commitment.

* **Control the number of concurrent compile requests**

  You can now [tune how many concurrent compile requests](./config_orchestration.html#tuning-orchestrator-concurrent-compile-requests) can be outstanding to the Puppet master, across all orchestrator jobs.

* **Puppet orchestrator `--url` flag renamed to `--service-url`**

  The `--url` flag has been renamed to `--service-url`. The `--url` flag has been aliased to `--service-url`, so you can continue to use it in your code if needed.

### Code Manager enhancements

* **Code Manager supports separate private keys for multiple Git repos**

  If you use multiple Git repositories with Code Manager, you can set a separate private key for each repo. To support this change, the `private-key` setting is deprecated in favor of the `git_settings` parameter. For details about this setting, see [customizing your Code Manager configuration](./code_mgr_custom.html).

### Razor enhancements

* **Functionality restored for Ruby versions 1.8.7 and later**

  Razor client versions later than 0.15.1 failed to install on client systems using end-of-life Ruby versions, including Centos 6. Installation attempts on Ruby versions between 1.8.7 and 1.9.3 resulted in failure to communicate with the server. As a workaround, the Razor client had to be installed with the built-in version of Ruby located at `/opt/puppetlabs/puppet/bin/ruby`. In this release, functionality has been restored for Ruby versions 1.8.7 and later.

* **Client version displays regardless of connectivity**

   In previous versions, `razor --version` displayed the client version only if the client could connect to the Razor server. In this version, client information is displayed regardless of connectivity to the server.

* **Workflow simplified for installing the Razor client on Windows**

  This version adds a supported path for installing the Razor client on Windows systems. See [Install the Razor client](./razor_install.html#install-the-razor-client) for details. For information about the Razor client, see [Using the Razor client](./razor_client.html).

* **Unsigned drivers allowed as an argument in the Windows build script**

   In previous versions, in order to use unsigned drivers in the WinPE image, you had to manually modify the build-razor-winpe.ps1 script. This version adds the ability to include unsigned drivers using an `-allowunsigned` flag in the build script. See [Build a WinPE image](./razor_windows_install.html#build-a-winpe-image) for details.

### Platform Enhancements

* **New supported Puppet agent platforms**

   This release adds the following Puppet agent platforms:

   * Fedora 23 (i386/x86_64)
   * Ubuntu 15.10 (i386/amd64)
   * Windows 10 (x64)

## Deprecations in PE 2016.1.1

### `puppet-job show` replaces `puppet-job list`

In this version of PE, the `puppet-job show` command replaces the `puppet-job list` command. The `puppet-job list` command has been aliased to the `puppet-job show` command. The orchestrator will raise a deprecation warning when you use the `puppet-job list` command.

### Relocated user-specified client configuration file for Puppet orchestrator

The user-specified client configuration file for the Puppet orchestrator has moved to `~/.puppetlabs/client-tools/orchestrator.conf`. It was previously located at `~/.puppetlabs/etc/puppet/orchestrator.conf`. Move your file to the new location. Using a config file in the old location will raise a deprecation warning.

