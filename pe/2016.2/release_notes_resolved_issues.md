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

> **Tip:** This PE release contains several components that have additional release notes. Refer to [Related release notes for this version of PE](./release_notes.html#related-release-notes-for-this-version-of-pe) for more information. 

## Resolved issues in PE 2016.2.1

### Upgrades to 2016.2 put all nodes in the Production environment group

Upgrades to the 2016.2 series ensure the default rule (matching all nodes) is present on the **Production environment** node group.

If you upgraded to PE 2016.2, and had previously added environment groups that did not inherit from the **Production environment** group, nodes in that group would fail to compile, as they would be in more than one environment. <!--PE-16374-->

### A long value in the PE license file caused module installation errors

This release fixes an issue where if there was a long value in the "to" field of the PE license.key file, then trying to install modules with the `puppet module` command caused an "400 Invalid Request" error. <!--CODEMGMT-768-->

### Puppet Server failed to start after errors on file sync's initial run

This release resolves an error that could occur the first time file sync ran on a master and that prevented Puppet Server from starting. <!--PE-16144-->

### Example `pe.conf` used incorrect setting for DNS alt names

The example `pe.conf` file that shipped with the previous version of PE used the incorrect setting for DNS alt names.

The correct setting is "pe_install::puppet_master_dnsaltnames". <!--PE-16337-->

### Running `puppet enterprise configure` about empty PE Infrasture node group caused errors

Previously, if you unpinned nodes from the PE Infrastructure node groups, you could not run the `puppet enterprise configure` command. <!--PE-16411-->

### Directory service setting overwritten on upgrade 

During upgrades between the 2015.3 series, the 2016.1 series, and 2016.2.0, the `disable_ldap_matching_rule_in_chain` directory service setting reverted to its default (`false`) setting. This issue has been resolved, and user settings are preserved on upgrade. <!--PE-16354-->  

### Installer help referenced non-functional no-op mode

The PE installer help referenced no-op mode, which is not yet functional for the installer/upgrader in the 2016.2 series. <!--PE-16449-->

### Ubuntu 16.04 agent upgrades could not use upgrade module

In the previous version of PE, you could not upgrade Ubuntu 16.04 agents using the puppet_agent upgrade module. This issue has been resolved.<!--PE-16317-->

### Sending empty node list to `groups/<id>/pin` endpoint caused future errors

This release corrects an issue in which if you sent an empty node list to the `/groups/<id>/pin` endpoint, the service returned group data that did not match the schema. Any subsequent requests to the `/groups` endpoint returned an error, and the PE console could no longer be used. <!--PE-16179-->

### Production code deploys to new Puppet masters required environment cache flush

Previously, if you had a **new** Puppet master, or a **new** LEI environment, the first time you used Code Manager and file sync to deploy code in the `production` environment, you needed to make a second code deploy before the code was available to Puppet Server and the node classifier. <!--PE-16367-->

### Static catalogs could not be globally disabled

This release corrects an issue in which you could not [globally disable static catalogs with Hiera](./static_catalogs.html#disabling-static-catalogs-globally-with-hiera). <!--PE-16471--> 

### Events page loading timed out in some large PE deployments

In some cases, very large PE deployments running PE 2016.2.0 received a timeout error when attempting to load the Events page in the PE console. This issue has been resolved. <!--PE-16383-->      

## Resolved issues in PE 2016.2

### The pe-puppetserver service did not complete its restart when enforcing changes on master of masters

Previously, if you made a configuration change to the master of masters (MoM), the Puppet agent run that enforced the change on the MoM would fail because the pe-puppetserver service had not completed its restart. <!--PE-14569-->

### Node classifier status endpoint returned an error

The node classifier status endpoint returned an error and an unknown state when queried at the default detail level. This issue is resolved in this release.

### Razor didn't consistently match MAC addresses

In previous releases, the `macaddress` fact wasn't correctly matched to nodes when multiple interfaces on the node reported different MAC addresses. The new `has_macaddress` operator searches all facts with the `macaddress` prefix, enabling better matching of [tags](./razor_tags.html). We recommend using the `has_macaddress` operator instead of the `macaddress` fact alone. (RAZOR-732)

### Code Manager would not deploy modules with long filenames

Previously, when installing Forge modules in which the path plus the file name in the module's tar file was longer than 100 characters, r10k did not copy the modules into the final module path. Because of this, Code Manager silently failed to deploy these modules into code-staging. <!--CODEMGMT-659-->


### Code Manager timeout for deployments was set lower than stated

The documented default for the Code Manager `deploys_timeout` parameter is 600 seconds, but PE was setting it at 300. The setting now correctly defaults to 600. <!--CODEMGMT-712-->

### File sync overwrote hiera.yaml

In the previous release, Code Manager runs caused file sync to overwrite the `hiera.yaml` file located in the codedir (`/etc/puppetlabs/code`). In this release, the default location for `hiera.yaml` is changed to `/etc/puppetlabs/puppet/hiera.yaml`.

On upgrades, the existing `hiera.yaml` file is automatically moved to the new location, **unless** you have modified the file. If you have modified the `hiera.yaml` file, move it to the new location. Do not leave a copy of `hiera.yaml` in the code directory. Future upgrades will fail if `hiera.yaml` is detected in the old location. <!--PE-13367--> <!--HI-490-->

### MCollective facts did not populate on agent installations

Before this fix, MCollective metadata facts did not correctly populate when you initially installed Puppet agents. <!--PE-12827-->

### Cached catalog run statuses were not accurately displayed in the PE console

In previous versions, if the intended catalog failed to compile on a node with `usecacheonfailure` set to true, the Puppet run would be reported as successful in the PE console, without alerting the user that a cached catalog was used. <!--PE-2160-->

### Information for multiple containment parents missing from node graph details view

In PE 2016.1, when examining node resources with more than one containment parent in the node graph, the class containment view's details pane listed information for only one parent. <!--PE-14508-->







