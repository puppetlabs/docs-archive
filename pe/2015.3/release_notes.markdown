---
layout: default
title: "Puppet Enterprise 2015.3 Release Notes"
canonical: "/pe/latest/release_notes.html"
---

This page describes new features and general improvements in the latest Puppet Enterprise (PE) release.

For more information about this release, see [Known Issues](./release_notes_known_issues.html) and [Security and Bug Fixes](./release_notes_security.html).

> **Tip:** This PE release contains several components that have additional release notes. Refer to [Related release notes](./release_notes_security.html#related-release-notes-for-this-version-of-pe) for more information.

## New Features and Improvements in PE 2015.3.3

PE 2015.3.3 is a release to fix a critical bug. For more information, review [the release notes security documentation](./release_notes_security.html).

## New Features and Improvements in PE 2015.3.2

PE 2015.3.2 is primarily a release to fix bugs and provide security improvements. For more information, review [the release notes security documentation](./release_notes_security.html).

### Support for Puppet Agents on Cisco IOS-XR Platform for Network Switches

Puppet Enterprise supports the Cisco IOS-XR platform for running Puppet agents on their network switches. Refer to the [Installing Cisco IOS-XR Agents](./install_iosxr.html) and the [puppetlabs-ciscopuppet module](https://forge.puppetlabs.com/puppetlabs/ciscopuppet) for more information.

### Code Manager Webhook Support for Team Foundation Server and Bitbucket

The Code Manager webhook endpoint now supports Git repositories hosted on Team Foundation Server (TFS) or Bitbucket. See the Code Manager [webhook](./code_mgr_webhook.html#query-parameters) documentation for details.

## New Features and Improvements in PE 2015.3.1

PE 2015.3.1 is a release to fix a critical bug. For more information, review [the release notes security documentation](./release_notes_security.html).

## New Features in PE 2015.3

### Application Orchestration

PE 2015.3 introduces Application Orchestration, a new Puppet app that provides Puppet language extensions and command-line tools to help you configure and manage multi-service and multi-node applications.

Specifically, Application Orchestration is:

- Extensions of the Puppet language for describing configuration relationships between components of a distributed application.
- A service that orchestrates configuration enforcement in a directed manner from the node level to the environment level.
- A command-line tool for executing orchestration jobs, inspecting those jobs, and understanding application instances declared in an environment.

For more information, refer to the following documentation:

- [Application Orchestration: Key Concepts and Workflow](./app_orchestration_workflow.html)
- [Puppet Orchestrator](./orchestrator_intro.html)

### Puppet Enterprise Client Tools

The Puppet Enterprise client tools package collects a set of command line tools that let you access Puppet Enterprise services from a workstation that may or may not be managed by Puppet.

The package includes clients for the following services:

- **Puppet Orchestrator**: Tools that provide the interface to the Application Orchestration service. These tools include `puppet-job` and `puppet-app`.
- **Puppet Access**: A client used to authenticate yourself to the PE RBAC token-based authentication service so that you can use other capabilities and APIs.
- **Razor**: The client for Razor, the provisioning application for deploying bare metal systems.

For instructions on installing the client tools, see [Installing the PE Client Tools Package](install_pe_client_tools.html).

### Token-Based Access

You can now access PE key features and service APIs using authentication tokens. Authentication tokens are generated per user and are tied to the user permissions configured through Role-Based Access Control (RBAC). The new Puppet Access command line tool allows you to quickly generate and manage authentication tokens. For more information, see [Token-Based Authentication](./rbac_token_auth.html).

### Compile Masters Supported in Monolithic Installations

PE users with large (or growing) infrastructures can now add compile masters to monolithic installations. Previously compile masters were only supported in split installations.

For instructions on installing compile masters, refer to the [compile master installation documentation](./install_multimaster.html).

For more information about installation sizes and hardware recommendations, see the [system requirements](./install_system_requirements.html#hardware-recommendations).

### New Agent Platform Support

This PE release includes support for the following agent platforms:

- Mac OS X 10.11 (El Capitan) (x86_64)
- Fedora 22 (i386 and x86_64)

### Run Puppet From the PE Console

You can now run Puppet on a specific node at any time from the PE console. The **Run Puppet** button is available in the node details view. You can use it to immediately test changes without having to log in to the node. For more information, see [Running Puppet on a Node](./console_classes_groups_running_puppet.html).

### Code Manager and File Sync

Code Manager and file sync work together with r10k to automate the management and deployment of your new Puppet code. Push your code updates to your Git repository, and then Puppet creates environments, installs modules, and deploys and syncs the new code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.

File sync, which is part of Puppet Server, creates a new code staging directory (`/etc/puppetlabs/code-staging`) so that it can sync your Puppet code to all masters. If you use Code Manager or file sync, you'll place your code into this new directory. If you are not using Code Manager or file sync, continue putting code into your usual codedir. 

See [Code Manager](./code_mgr.html) for more information about how to get started with these code management tools.To learn more about file sync and the staging directory, see [About File Sync](./cmgmt_filesync.html).

## Improvements in PE 2015.3

### Permissions in the Operators and Viewers Roles Can Now Be Changed

In previous releases of PE, the Operators and Viewers roles in Role-Based Access Control (RBAC) came with a default set of permissions that could not be changed. In this release, the default permissions for the Operators and Viewers roles can be changed by any user with the **User roles - Edit** permission. For more information on user roles, see [Creating and Managing Users and User Roles](./rbac_user_roles.html).

### Status API No Longer in Tech Preview

[Status endpoints](./status_api.html) were added in PE2015.2, but they were considered to have a tech preview status. These endpoints are now fully supported and are no longer in tech preview.

### Unsigned Certificates is Now a Separate Page in the PE UI

In previous releases, certificates were signed in the PE UI from the **Unsigned certificates** tab, which was part of the **Inventory** page. In PE 2015.3, **Unsigned certificates** has been moved from a tab to a full page to make it easier to access. To sign certificates in the PE UI, go to **Nodes** > **Unsigned certificates**.

### Configuration Management Improvements

With this release, Configuration Management has the following improvements:

+ Ability to filter on tags to view resources by module and to locate exported resources when using the node graph.
+ Code coordinates on the node graph link to a resource's event details.
+ Restored reporting on unchanged events for audit purposes.
+ New paginator in **Reports** that better handles large numbers of records.

### Filebucket Resource No Longer Created By Default

Puppet Enterprise used to create a filebucket resource by default, which was not necessary and led to problems for customers. As of PE 2015.3, we no longer create a file bucket and we set a resource default of `backup => false` for all files.

If you want to enable the file bucket for your deployment, see the [filebucket type reference](/references/latest/type.html#filebucket).

### Sort Capabilities Added to Event Details

All Event tables can now be sorted.

### Reports Can Be Filtered

You can now filter reports by fact value or report run status.

### r10k Creates a Record of the Last Code Deployment

If the code deployed by r10k is copied to another location without the cached repos, it becomes impossible to use Git to interact with the repository and see which version of code r10k deployed. R10k now creates a .r10k-deploy.json file that records the time and SHA of the last code deployment.

### `r10k puppetfile install` Supports New Options

The r10k puppetfile install subcommand was able to set a custom puppetfile path and moduledir location via environment variables to match librarian-puppet, but they didn't match the librarian-puppet semantics. The `r10k puppetfile install` [subcommand](./r10k_reference.html#puppetfile) now supports command line flags to set these options.

### r10k Data Format Output Improved

The r10k deploy display output format was non-standard. It has been improved and now defaults to YAML.

### New PE-Only Modules

#### Azure

The [azure module](https://forge.puppetlabs.com/puppetlabs/azure) allows you to drive the Microsoft Azure API using Puppet code. Use Puppet to create, stop, restart, and destroy Virtual Machines, meaning you can manage even more of your infrastructure as code.

#### PowerShell

The [powershell module](https://forge.puppetlabs.com/puppetlabs/powershell) adds a PowerShell provider that can execute PowerShell commands. This module is particularly helpful if you need to run PowerShell commands but don't know the details about how PowerShell is executed, since you can technically run PowerShell commands in Puppet without the module.

#### WSUS

The Windows Server Update Service (WSUS) lets Windows administrators manage operating system updates using their own servers instead of Microsoft's Windows Update servers. The [wsus_client](https://forge.puppetlabs.com/puppetlabs/wsus_client) module configures Puppet agents to schedule update downloads and installations from a WSUS server, manage user access to update settings, and configure automatic updates.

## Deprecations and Removed Features in PE 2015.3

### Accounts Module Removed

The built-in pe_accounts module has been removed from Puppet Enterprise. It is replaced by the new [accounts module](https://forge.puppetlabs.com/puppetlabs/accounts), which is available for download from the Puppet Forge. The module manages resources related to login and service accounts.

### Windows 2003 Removed

Windows 2003 and 2003r2 are no longer supported in Puppet Enterprise.

