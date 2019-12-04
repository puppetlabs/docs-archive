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

## Resolved issues in PE 2017.2.5

### Windows agent installer errored with multiple config options

The Windows simplified agent installer script errored out if you provided more than one configuration option. This issue has been resolved, and now PowerShell parameters, as well as multiple configuration options, can be passed in any order. <!--PE-22478-->

## Resolved issues in PE 2017.2.4

### Agent installation and upgrade failed on Solaris nodes when using wget

Frictionless agent installation and upgrade failed on Solaris nodes if wget is used instead of curl. <!--PE-22024-->

### Windows agent supports data execution prevention

Windows agents have to been updated to include security enhancements for data execution prevention. The agent also corrects space layout randomization. <!--PE-21053-->

## Resolved issues in PE 2017.2.3

### ActiveMQ configuration file couldn't be repaired

If the ActiveMQ configuration file, `activemq.xml` was removed or damaged, PE couldn't replace or repair the file. <!--PE-17452-->

### Agent packages for AIX, Solaris, OSX, and Windows weren't synched correctly to compile masters

Applying any of the `pe_repo::platform::aix_*` classes on a compile master that couldn't reach `pm.puppetlabs.com` resulted in a failure. <!--PE-21356-->

### Nodes with no reports generated console errors on report retrieval

If you clicked a node's report list, and the node didn't have any reports stored in PuppetDB, the console returned an error that it couldn't retrieve the report. <!--PE-21458-->

### Web-based installation in browsers set to Japanese still displayed in English

If the browser was set to `ja-JP`, the web-based installer displayed text in English. <!--PE-20907-->

### Catalog compilation failed with older versions of the Puppet inifile module

Using a Puppet inifile module earlier than version 1.1.0 conflicted with the PE inifile module, resulting in catalog compilation failures. <!--PE-9868-->

## Resolved issues in PE 2017.2.2

### Compile masters couldn't retrieve certain agent packages

Enterprise Linux and Ubuntu agent packages weren't distributed to compile masters as expected through the `pe_repo` class. <!--PE-20749-->

### Custom environments for preconfigured node groups were re-set on upgrade

If you changed the environments of preconfigured node groups, upgrading re-set the environments to production. <!--PE-18107-->

### Orchestrator did not report correct job status if compile master restarted

If a compile master was restarted during an orchestrator job, the job statuses for agent nodes reporting to the compile master were all reported as "In progress." The orchestrator now reports jobs status correctly if a compile master is restarted.
<!--ORCH-1676-->

### Console stops responding when navigating new group in separate window

In some cases, the PE console would stop responding if you tried to navigate a newly created node group in a separate tab or window while the group was still open in the original tab or window. <!--PE-20884-->

### Incorrect response on `/orchestrator/v1/inventory` endpoint

`POST` requests to the `/orchestrator/v1/inventory` endpoint without a body/payload erroneously returned a `200` response. An invalid body for `POST` now correctly returns a `400` response. <!--ORCH-1544-->

## Resolved issues in PE 2017.2

### PE installed newer puppet-agent package

Previously, PE would install a newer `puppet-agent` package than expected if the Puppet Collection 1 (PC1) open source Puppet repos were set up on the system. In this release, the installer installs the specific version of the `puppet-agent` package that is expected.

### Web-based installer didn't support SLES 12 (SP1, SP2)

Previously, the web-based installer wasn't supported on SLES 12 (SP1 or SP2), so you had to use the text-mode installation method. Web-based installation is now supported. <!--PE-19513-->

### Upgrade failed if a `PE Infrastructure` node group name contained unexpected capitalization

Previously, installation failed if you changed the default capitalization for any `PE Infrastructure` node group names, because the installer was case insensitive. For example, if the installer encountered a `PE Mcollective` (lowercase "c") node group when it expected a `PE MCollective` (uppercase "c") node group, it returned an error about non-unique group names. <!--PE-18390-->

### Installation failed on systems that used UTF-8 characters in file names

If you attempted to install Puppet Enterprise on a system that used UTF-8 characters in path or file names, installation failed with a message indicating errors in the pe.conf file. <!--PE-18165-->

### Code Manager deploys timed out due to a script hanging indefinitely

In PE 2016.5 and 2017.1, Code Manager deploys hung and ultimately timed out for some users with a large number of native types in a single environment. This issue was caused by the generate-puppet-types.rb script hanging indefinitely during the pre-commit phase. <!--CODEMGMT-1055-->

### On Windows, `puppet-code deploy` failed for tokens with UTF-8 characters

On Windows systems, `puppet-code deploy` failed if you specified an authentication token that includes UTF-8 characters in the filename. This failure occurred only if you specified the token in the `puppet-code.conf` file or on the command line with the `--token-file` option. <!--CODEMGMT-1026-->

### Overview load time for deployments with a large number of facts

Previously, the **Overview** page in the PE console loaded very slowly for some deployments with a large number of facts. Page load time has been significantly decreased in this version. <!--PE-18901-->

### Discard changes did not clear changes made to user roles

In PE 2016.5 and 2017.1, clicking **Discard changes** failed to clear pending user changes when altering user roles in the console. <!--PE-20300-->

### Correct URL and page rendering when cancelling browser navigation

In previous versions of PE, using your browser's back button would rewrite the URL to the previous page (if the previous page was in the console). If you cancelled this navigation, the URL would read incorrectly, and a refresh could potentially render the incorrect page. The URL will now render the correct page if you cancel the navigation. <!--PE-20012-->
g
### RBAC certificate whitelist didn't reload when pe-console-services sent a HUP signal

Prior to this release, if pe-console-services was sent a HUP signal, the RBAC service would fail to reload the certificate whitelist, effectively caching the content at original startup. With this release, the certificate whitelist is reloaded when a HUP is received. <!--PE-19535-->

### Node filtering not respected by pagination

In previous releases, if a filter was applied to the node list inventory, the filter was not carried over into subsequent pages.
<!--PE-19487-->

### CSV downloads in filtered views raised authentication errors

Previously, if you tried to download CSV tables from filtered views in the console, the console would raise an authentication error. <!--PE-20257-->

### MCollective uses YAML.safe_load as default

The AES encryption and SSL security plugins now use **YAML.safe_load** to deserialize messages. This prevents potential attack vectors from deserializing YAML into classes. These plugins now require Ruby 2.1 or later to use YAML deserialization. <!--MCO-794-->

### PuppetDB could not store sensitive parameters

This release corrects an issue in which adding the [sensitive data type]({{puppet}}/lang_data_sensitive.html#language:-data-types:-sensitive) to the parameters of an existing resource caused catalog storage failures in PuppetDB. <!--DOC-3123, PDB-3322-->

### PuppetDB filters null bytes in Resource Events fields

Previously, PuppetDB was unable to properly handle entries in the Windows registry that contained a default value of `'\0'`.
<!--PDB-3058-->


