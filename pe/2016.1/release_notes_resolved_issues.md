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

> **Tip:** This PE release contains several components that have additional release notes. Refer to [Related release notes](#related-release-notes-for-this-version-of-pe) for more information.

## Resolved issues in PE 2016.1.2

### Classifier service showed an error in status monitor

In the Puppet services status monitor, the classifier service showed an error. <!--PE-15252-->

### Code Manager sometimes crashed Puppet Server on r10k to Code Manager upgrades

If you previously deployed code with r10k and then upgraded from r10k to Code Manager, deploying code with Code Manager could crash Puppet Server. This happened if r10k left Git files behind in the live code directory.

### File sync was always disabled on upgrades

In PE 2016.1.1, the file_sync_enabled parameter (on the `puppet_enterprise::profile::master` class) was always set to `false` on upgrades. To use file sync, users had to set the parameter to `true` after upgrading. <!--PE-15183-->

### Code Manager did not authenticate webhooks

In the previous release, Code Manager did not respect the set value of the `authenticate-webhook` setting and always assumed this value was `false`. This meant that the `/v1/webhook` endpoint did not require or validate authentication tokens for any request. <!--PE-15162-->

### Vague Code Manager error message improved for all deployment operations

In 2016.1.1, we improved the error message for insufficient private key permissions. However, Code Manager still logged the old vague error message when performing a deployment of all environments. <!--CODEMGMT-666-->

### Code Manager did not deploy modules with long filenames

When installing Forge modules in which the path plus the file name in the module’s tar file is longer than 100 characters, r10k did not copy the modules into the final module path. Because of this, Code Manager silently failed to deploy these modules into the `code-staging` directory. <!--CODEMGMT-660-->

### Code Manager and r10k could not download PE-only modules with a proxy

Previously, if you accessed Puppet Forge via a proxy, Code Manager and r10k could not install and manage PE-only modules. This change resolves that issue. <!--RK-229-->

### Puppet Server restarted when PuppetDB queried compile masters

In PE infrastructures using compile masters, the `puppetdb_query`, which defines Puppet masters in PuppetDB had different results for each compile master. This caused the  `pe-puppetserver` service to restart on every Puppet run. <!--PE-15048-->

### Puppet Enterprise's use of PuppetDB queries returned inactive nodes for active node queries

Previously PE used the `puppetdb_query` incorrectly when querying nodes assigned as compile masters, and returned results of deactivated nodes alongside active nodes. <!--PE-15200-->

### PuppetDB `sync_ini` class caused unnecessary restarts

Previously, in multiple PuppetDB node configurations, the `puppet_enterprise::puppetdb::sync_ini` class, which queries PuppetDB for all PuppetDB nodes, was misconfigured and caused PuppetDB to restart every time Puppet ran. This occasionally enforced erroneous changes. <!--PE-15193-->

### After upgrades OS X and Solaris 11 pxp-agents needed restart

After upgrading OS X and Solaris 11 agents, the pxp-agent on those nodes needed a restart to properly perform orchestrator runs. <!--14320-->

To restart the PXP agent service, run the following commands:

### Orchestration database user not set on upgrade

During upgrades to PE 2016.1.1, the Orchestration database user was not properly set, so `pe-orchestration-services` could not properly install. This left the service unavailable after upgrading.

<!--PE-15041-->

### Invalid regex rules resulted in a PuppetDB connection error

Entering a `matches regex` rule with an invalid expression erroneously resulted in a PuppetDB connection error. <!--PE-15027-->

### Node classifier status endpoint returned an error

The node classifier status endpoint returned an error and an unknown state when queried at the default detail level. <!--PE-14857-->

### Razor didn't consistently match MAC addresses

In previous releases, the `macaddress` fact wasn't correctly matched to nodes when multiple interfaces on the node reported different MAC addresses. The new `has_macaddress` operator searches all facts with the `macaddress` prefix, enabling better matching of [tags](.razor_tags.html). We recommend using the `has_macaddress` operator instead of the `macaddress` fact alone. (RAZOR-732)

## Resolved issues in PE 2016.1.1

### AIX agent upgrades failed to restart services

Previously, when you upgraded AIX agents, Puppet services would fail to properly restart.

### Classes removed from manifest continued to appear in console after refresh

In previous releases, classes removed from the Puppet server continued to appear in the console after clicking the refresh classes button. With this release, refreshing updates the list of classes to match those available on the server.

### Vague error message logged for insufficient Code Manager private key permissions

If, during Code Manager configuration, you specified a private key that didn't have sufficient permissions for Code Manager to access the file, a vague error message was logged after deployment. In this release, this problem causes failure earlier and gives a more helpful error message.

### Setting 'n' in answer file to disable file sync on install broke classification

In 2015.3, if you used an answer file to install and wanted to disable file sync, you could not use `q_puppetmaster_file_sync_service_enabled=n` in the answer file, as doing so created classification failures in the PE node groups.

### Formatting of exported CSV files was displayed incorrectly in some Windows editors

In PE 2015.3, use of the line feed (LF) new line delimiter caused CSV files exported to Windows to display line breaks incorrectly in some editors. To correct this issue, the new line delimiter has been changed to carriage return plus line feed (CR+LF), in keeping with CSV standards.

### Console data was exported in an inconsistent format

When exporting data from the PE console, it is typically exported in text/csv format. However, when exporting data from **Nodes** > **Inventory** > &lt;ANY NODE&gt; > **Reports** > **Export data** in PE 2015.3, the data was exported with an `application/json` MIME type. In PE 2016.1.1, the export format is text/csv for all data exports from the PE GUI.

### Node classifier translate endpoint returned an unexpected error on false queries

In previous releases, if you supplied `false` as a query to the node classifier translate endpoint, the classifier responded with a 500 error. In this release, the same query returns the expected 400 error, indicating a bad request.

### Razor exposed unused management ports

In previous releases, the Razor server exposed management ports on the local interface that could deserialize Java objects. In this release, unused management ports are closed by default to minimize the exposure risk to generic Java unserialize vulnerabilities. <!--RAZOR-733-->

### Razor `protect-new-nodes` didn't match tags to nodes booted into the microkernel

In previous releases, setting `protect-new-nodes` to `true` prevented tags from matching nodes that were booted into the microkernel. Without knowing which tags matched a node, it was unclear which policy the node would be eligible for when the `reinstall-node` command was issued. In this release, protected nodes that are booted into the microkernel are matched with tags. (RAZOR-779)

### Razor `delete-repo` command didn't remove the repository folder from the server

In previous releases, the `delete-repo` command removed the repository from the Razor database, but didn't delete downloaded files on disk. In this release, if you supplied the `iso_url` property when you created the repository, the repository folder is also deleted from the server. (RAZOR-757)

## Related release notes

### Puppet agent release notes

This version of PE includes Puppet agent version 1.4.2. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html#puppet-agent-142) for more information.

### PuppetDB release notes

This version of PE includes PuppetDB version 4.0.1. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

### Puppet Server release notes

This version of PE includes Puppet Server version 2.3.1. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.

### Puppet release notes

This version of PE includes Puppet version 4.4.2. Refer to the [Puppet release notes]({{puppet}}/release_notes.html) for more information.

