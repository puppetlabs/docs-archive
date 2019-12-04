---
layout: default
title: "Resolved issues"
canonical: "/pe/latest/release_notes_resolved_issues.html"
---

This page describes resolved issues in the latest Puppet Enterprise (PE) release.

For more information about this release, see:

* [Security and vulnerability announcements](/security)
* [New features and enhancements](./release_notes.html)
* [Known issues](./release_notes_known_issues.html)

> **Tip:** This PE release contains several components that have additional release notes. Refer to [Related release notes](./release_notes.html#related-release-notes) for more information.

## Resolved issues in PE 2016.4.13

### Critical corruptive permissions issue with Windows installer for Puppet agents 1.10.13, 5.3.7, and 5.5.2

Due to a critical issue with the Windows installer, Puppet agents 1.10.13, 5.3.7, and 5.5.2 could inadvertently change permissions on files across a Windows node's filesystem during installation or upgrade.

### Improved response times for node classification

An internal cache of the classes has been added to improve the response times from the classifier when creating and updating node groups. In previous releases of Puppet Enterprise, installations with large numbers of environments sometimes experienced slow response times.

### Upgrades failed on compile masters set to use cached catalogs

If `use_cached_catalogs` was set to `true` on a compile master, upgrade failed. <!--PE-24423-->

## Resolved issues in PE 2016.4.10

### External directory service searches nested groups by default

The external directory service no longer searches nested groups by default; the directory service connection setting `search_nested_groups` has a default value of `false`. To search for groups that are members of an external directory group, you must change this value to `true`.

In PE 2015.3 and earlier versions, if you connected with an external directory service to work with users and groups already established in your directory, the default behavior was to search nested groups. When upgrading from one of these earlier versions, this default setting is preserved. For organizations with a large number of nested group memberships, this can slow performance. To avoid performance issues, change the `search_nested_groups` field to `false` for a more shallow search in which RBAC only searches the groups it has been configured to use for user roles. <!--PE-22428-->

### Enterprise Linux 7 AArch64 upgrades using the puppet_agent module failed

Enterprise Linux 7 AArch64 agent upgrades using the puppet_agent module failed to upgrade the agent on the target system. <!--PE-23558-->

### Error launching MCollective after installing Puppet agents on IBM POWER8

Puppet agents running RHEL 7 or Ubuntu 16.04 on IBM POWER8 hardware did not use Ruby gems correctly, preventing the MCollective service from starting. <!--PE-21713-->

## Resolved issues in PE 2016.4.9

### Windows agent installer errored with multiple config options

The Windows simplified agent installer script errored out if you provided more than one configuration option. This issue has been resolved, and now PowerShell parameters, as well as multiple configuration options, can be passed in any order. <!--PE-22478-->

### PE 2016.4 Puppet Server logs were filled with `gettext` warnings

On a fresh install of PE 2016.4.8, the Puppet Server logs were filled with repeated warnings about `gettext` not being available. These messages were continually produced and accounted for over 90% of the log messages created by an idle PE Puppet Server. This issues is now fixed. <!--PE-22445-->

### PCP broker `auth.conf` allowed unnecessary requests

Orchestration communication via PCP now denies all unknown message types.

## Resolved issues in PE 2016.4.8

### Agent installation and upgrade failed on Solaris nodes when using wget

Frictionless agent installation and upgrade failed on Solaris nodes if wget is used instead of curl. <!--PE-22024-->

### Windows agent supports data execution prevention

Windows agents have to been updated to include security enhancements for data execution prevention. The agent also corrects space layout randomization. <!--PE-21053-->

## Resolved issues in PE 2016.4.7

### ActiveMQ configuration and PE classification weren't synched after upgrade from 2015.2.1 or earlier

When upgrading from 2015.2.1 or earlier, where hub and spoke installations could be configured without setting `activemq_hubname`, ActiveMQ configuration and PE classification could get out-of-sync. This desynchronization caused hub-and-spoke to be misconfigured if `activemq.xml` was regenerated from a fresh base file.

With this version, if `activemq_hubname` is not set, hub and spoke connections are inferred from nodes with the `Puppet_enterprise::Profile::Amq::Hub` class.<!--PE-21332-->

### Agent packages for AIX, Solaris, OSX, and Windows weren't synched correctly to compile masters

Applying any of the `pe_repo::platform::aix_*` classes on a compile master that couldn't reach `pm.puppetlabs.com` resulted in a failure. <!--PE-21356-->

### Nodes with no reports generated console errors on report retrieval

If you clicked a node's report list, and the node didn't have any reports stored in PuppetDB, the console returned an error that it couldn't retrieve the report. <!--PE-21458-->

### Catalog compilation failed with older versions of the Puppet inifile module

Using a Puppet inifile module earlier than version 1.1.0 conflicted with the PE inifile module, resulting in catalog compilation failures. <!--PE-9868-->

## Resolved issues in PE 2016.4.6

### Compile masters couldn't retrieve certain agent packages

Enterprise Linux and Ubuntu agent packages weren't distributed to compile masters as expected through the `pe_repo` class. <!--PE-20749-->

### Custom environments for preconfigured node groups were re-set on upgrade

If you changed the environments of preconfigured node groups, upgrading re-set the environments to production. <!--PE-18107-->

### Use of r10k log level debug2 caused an “invalid configuration” error

In PE 2016.4.5, setting the r10k log verbosity level to `debug2` resulted in a `Code Manager could not be started due to invalid configuration` error. <!--PE-20583-->

### Installer hung if minimum system requirements weren't met

The graphical installer hung when verifying the target host's CPU, memory, and disk space if those components were below recommended thresholds. <!--PE-20570-->

### Console stops responding when navigating new group in separate window

In some cases, the PE console would stop responding if you tried to navigate a newly created node group in a separate tab or window while the group was still open in the original tab or window. <!--PE-20884-->

## Resolved issues in PE 2016.4.5

### PE installed newer `puppet-agent` package

Previously, PE would install a newer `puppet-agent` package than expected if the Puppet Collection 1 (PC1) open source Puppet repos were set up on the system. In this release, the installer installs the specific version of the `puppet-agent` package that is expected.

### Web-based installer didn't support SLES 12 (SP1, SP2)

Previously, the web-based installer wasn't supported on SLES 12 (SP1 or SP2), so you had to use the text-mode installation method. Web-based installation is now supported. <!--PE-19513-->

### Upgrade failed if a `PE Infrastructure` node group name contained unexpected capitalization

Previously, installation failed if you changed the default capitalization for any `PE Infrastructure` node group names, because the installer was case insensitive. For example, if the installer encountered a `PE Mcollective` (lowercase "c") node group when it expected a `PE MCollective` (uppercase "c") node group, it returned an error about non-unique group names. <!--PE-18390-->

### PuppetDB could not store sensitive parameters

This release corrects an issue in which adding the [sensitive datatype]({{puppet}}/lang_data_sensitive.html#language:-data-types:-sensitive) to the parameters of an existing resource caused catalog storage failures in PuppetDB. <!--DOC-3123, PDB-3322-->

### Enabling PuppetDB replicas could generate duplicate reports

In previous versions, enabling PuppetDB replication could generate the following duplicate reports error:

~~~
ERROR: duplicate key value violates unique constraint "reports_hash_expr_idx"
~~~

These duplicates didn't affect the storage of report data but did cause performance impacts and increased disk usage due to discarded duplicates. <!--PE-20115-->

### PuppetDB filters null bytes in Resource Events fields

Previously, PuppetDB was unable to properly handle entries in the Windows registry that contained a default value of `'\0'`.
<!--PDB-3058-->

### MCollective class filtering did not work on AIX agents

This release corrects an issue in which MCollective class filtering did not work on AIX agents.

### ActiveMQ didn't remove stored reply queues

In previous versions, ActiveMQ indefinitely stored queues used for MCollective replies. Each time a MCollective command was run, a small amount of memory leak would occur. This has been resolved in this release. <!--PE-19972-->

### Overview load time for deployments with a large number of facts

Previously, the **Overview** page in the PE console loaded very slowly for some deployments with a large number of facts. Page load time has been significantly decreased in this version. <!--PE-18901-->

## Resolved issues in PE 2016.4.3

### Agent applied cached catalog during split upgrades

Previously, during upgrades of split installations, an agent could attempt to get a catalog but fail, and then apply a cached catalog and interrupt the upgrade. <!--PE-16909-->

### Message for a `puppet code deploy -all` error was unhelpful

When running `puppet code deploy --all`, if a `collect-failed` error occured, the error message did not have information about the cause of the error, showing `details: null` instead. Now, if there is further information about the error, it is included in the `details` key in the JSON error response. <!--CODEMGMT-977-->

### Stack trace was shown in Code Manager log instead of timeout error message

When running `puppet code deploy --all`, if a timeout error occurred, the Code Manager log displayed a stack trace instead of a helpful error message. <!--CODEMGMT-969-->

### File sync failed if it found a .git directory in the live code dir

On upgrades from r10k to Code Manager, file sync failed if it encountered any `.git` directories, which r10k creates, in the live code directory. With this fix, you no longer need to manually remove .git directories you upgrade from r10k to Code Manager. <!--CODEMGMT-922-->

### Some MCollective symlinks not set correctly

This release resolves an issue in which some MCollective symlink paths were not set correctly.
<!-- PE-12874-->

## Resolved issues in PE 2016.4.2

### Large class lists caused PE console lag

Previously in PE, if you had a large number of classes and environments and tried to access the Node Manager in the PE console, you could experience browser lags or freezes. <!--PE-14708-->

### Opting out of data collection impacted the `check_for_updates` flag's setting

The `check_for_updates` flag was linked to the `send_analytics_data` flag in Puppet Server, such that the value set for `send_analytics_data` also automatically applied to `check_for_updates`, and vice versa. <!--PE-17701-->

## Node classifier did not properly update restored environments

In some cases, the node classifier didn't correctly update environments that were deleted and then restored, if the environment contained the same classes. This issue most often affected Code Manager users. <!--PE-15475-->

## Resolved issues in PE 2016.4

### PE installer no longer stores temp files `/tmp/ dir if `noexec`

In previous versions of PE, during installation, the installer would store some temporary files in the `/tmp` directory. If that directory was not executable, the install would fail. The installer now stores these temporary files in `/opt/puppetlabs/server/data/puppetserver/server_data` if the `/tmp` directory is not executable. <!--PE-8171-->

### Code Manager could develop a bloated cache with many short-lived branches

Code Manager did not support repository pruning. If you used many short-lived branches, Code Manager's cache could become bloated and take up a lot of disk space. Code Manager now performs garbage collection on cached repositories periodically to avoid this issue. <!--CODEMGMT-775-->

### Setting `strict_variables` to true caused an error

There was a disused parameter in the `puppet_enterprise::profile::master` class that caused an error if the Puppet setting `strict_variables` was set to true. The parameter has now been removed. <!--CODEMGMT-760-->

### Symlinks caused r10k to terminate with a non-zero exit code

Previously, if r10k encountered a symbolic link when deploying a Forge module to an environment, r10k logged an error, and then when the process finished, terminated with a non-zero exit code. It now logs a warning and exits 0 in this scenario. <!--RK-258-->

### `group-children` endpoint returned error for groups with no children

Calling the [`group-children` endpoint](./nc_groups_children.html) for a group with no children resulted in a server error, rather than the expected empty array. <!--PE-16200-->

### The Run Puppet option for individual nodes wasn't available in large installations

In installations with large numbers of nodes, the **Run Puppet** button didn't appear on the node detail pages. <!--PE-16727-->

### Underscores in node names resulted in error

When loading the node detail page for a node with an underscore in its name, an error appeared stating, "Error retrieving node agent list: Server Error." Additionally, the option to run Puppet didn't appear. <!--PE-14894-->

### Count of failed login attempts did not reset after reinstating a user account

In cases where a user's account was revoked due to too many incorrect login attempts, and the account was reinstated via the API by updating the `is_revoked` flag, the `failed_login_count` was not correctly reset, so the user's next incorrect login attempt re-revoked their account. <!--PE-16430-->

### Installer warned of unavailable version variables

This release corrects an issue in which the PE installer displayed errors that the `pe_version` and `pe_server_version` variables were undefined. <!--PE-16013-->

### SE Linux Puppet resources couldn't be managed in previous release

Previously, in PE, if you were running RHEL 6 or 7 agents, you couldn't manage SE Linux Puppet resources with Puppet agent. <!--RE-7660-->

### Console "not authorized" screen lacked a reload option

When a user lacking console access permission logged into the console, the "You are not authorized to access the console" screen did not facilitate logging out. A link to log out and return to the login screen has been added. <!--PE-11848-->





