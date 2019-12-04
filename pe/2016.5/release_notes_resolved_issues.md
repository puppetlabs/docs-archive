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

## Resolved issues in PE 2016.5.2

### Agent applied cached catalog during split upgrades

Previously, during upgrades of split installations, an agent could attempt to get a catalog but fail, and then apply a cached catalog and interrupt the upgrade. <!--PE-16909-->

### Added `git gc` operation for better performance

Prior to this release, the git repo that backs file sync would grow over time because it never had a `git gc` operation performed on it. This caused file sync operations to take longer than normal. 

This version of PE includes a newer version of the library that interacts with the git repo and automatically handles `git gc` operations to keep the repo optimized for maximum performance. <!--CODEMGMT-889-->  

## Resolved issues in PE 2016.5.1

### Message for a `puppet code deploy -all` error was unhelpful

When running `puppet code deploy --all`, if a `collect-failed` error occured, the error message did not have information about the cause of the error, showing `details: null` instead. Now, if there is further information about the error, it is included in the `details` key in the JSON error response. <!--CODEMGMT-977-->

### Stack trace was shown in Code Manager log instead of timeout error message

When running `puppet code deploy --all`, if a timeout error occurred, the Code Manager log displayed a stack trace instead of a helpful error message. <!--CODEMGMT-969-->

### Puppet Services status monitor caused frequent `file-sync-storage-service` timeouts

The `file-sync-storage-service` endpoint often timed out and posted a large stack trace to the Puppet Server log. The frequent calls made by the Puppet Services status monitor to the endpoint often caused this timeout. The default timeout period has been extended from five seconds to 30 seconds, and is now configurable in calls to the [status API](./status_api.html#json-endpoints).   

### Puppet runs with failed resource generation shown as successful in PE console

Previously, if a Puppet run failed to generate resources, the run status in the PE console indicated it was a successful run instead of a failed run. <!--PE-17901-->

### Agents can't send unknown message types

Orchestration communication via the Puppet Communications Protocol (PCP) now denies all unknown message types sent from Puppet agents. <!--ORCH-1552-->

### Code Manager didn't enforce usage pattern

Global options and action arguments were treated as indistinguishable. In this version, commands must follow the prescribed usage pattern: `puppet code [global options] <action> [action arguments]`.

### Error returned on token generation for new remote users 

If a remote user from an external directory group attempted to generate an authentication token with a specific expiry without first logging into the PE console, an error message was returned. <!--PE-12029-->

### Node graph displayed errors in names containing non-ASCII characters  

The node graph rendering tool read both individual non-ASCII characters and groups of non-ASCII characters as a single underscore. This lead to multiple graph elements receiving the same name, which were then conflated and incorrectly mapped. <!--PE-17096-->





