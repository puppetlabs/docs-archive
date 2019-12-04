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

## Resolved issues in PE 2017.1.1

### Code manager commands failed with directory service integrations

This release corrects an issue in PE 2017.1.0, in which Code Manager commands failed when issued by RBAC users from a directory service integration, such as LDAP. <!--PE-19909-->

### Windows agents did not pick up replica configurations

Previously, if you provisioned and enabled a replica Puppet master, Windows agents did not correctly pick up the configuration on the first Puppet run. Puppet had to run a second time before the agents could properly connect to the replica. <!--PE-18642-->


## Resolved issues in PE 2017.1

### File sync failed if it found a .git directory in the live code dir

On upgrades from r10k to Code Manager, file sync failed if it encountered any `.git` directories, which r10k creates, in the live code directory. With this fix, you no longer need to manually remove .git directories you upgrade from r10k to Code Manager. <!--CODEMGMT-922-->

### MCollective class filtering did not work on AIX agents

This release corrects an issue in which MCollective class filtering did not work on AIX agents.

<!--PE-19422-->

### Agent applied cached catalog during split upgrades

Previously, during upgrades of split installations, an agent could attempt to get a catalog but fail, and then apply a cached catalog and interrupt the upgrade.

<!--PE-16909-->

### Console did not show error on Puppet runs when Puppet server was stopped

If you attempted to run Puppet in the console while the pe-puppetserver service was stopped, the console did not show a relevant error message.

<!--PE-19075-->

### Web-based installer reported required memory incorrectly

In previous versions, the web-based installer could incorrectly report that the amount of RAM available on the target machine was not enough to install PE.

<!--PE-8784-->


