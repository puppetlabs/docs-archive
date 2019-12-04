---
layout: default
title: "New features, enhancements, and deprecations"
canonical: "/pe/latest/release_notes.html"
---

[tech preview]: /tech_preview/tech_preview.html

This page describes new features, enhancements, and deprecations in this Puppet Enterprise (PE) release.

For more information about this release, see:

* [Known issues](./release_notes_known_issues.html)
* [Resolved issues](./release_notes_resolved_issues.html)
* [Security and vulnerability announcements](/security)

> **Tip:** This PE release contains several components that have additional release notes. Refer to [Related release notes](#related-release-notes) for more information.

## New features in PE 2016.5.2

PE 2016.5.2 is primarily a release to fix bugs and provide security improvements. For more information, review the [resolved issues](./release_notes_resolved_issues.html) and [security vulnerabilities](/security) documentation.

## New features in PE 2016.5.1

### High availability

High availability for Puppet Enterprise ensures that your system remains operational even if certain infrastructure components become unreachable. With high availability enabled, Puppet runs fail over to a replica if your master or master of masters is unreachable. If your master is permanently disabled, you can promote the replica to serve as the new master.

**Note:** In this release, we don't support high availability for split installations – where the master, console, and PuppetDB components are installed on separate machines. Only monolithic installations - with or without compile masters and a master of masters - is supported.

For more information about high availability, see:

* [High availability overview](./ha_overview.html)
* [Configure high availability](./ha_configure.html)

## Enhancements in PE 2016.5.1

### Puppet orchestrator enhancements

* A new flag, `--no-enforce-environment`, ensures the orchestrator will ignore the environment set by the `--environment` flag for agent runs. When you use this flag with the `puppet job run` command, agents run in the environment specified by the PE Node Manager or their `puppet.conf` file.

### RBAC and activity service enhancements

* The **Run Puppet on agent nodes** permission now includes the ability to trigger a Puppet run from the PE console or orchestrator. See [RBAC available permissions](./rbac_permissions.html#available-permissions).

* The activity service event reporting now includes agent runs that are part of [orchestration jobs](./rbac_activityreporting.html#orchestration).

### Analytics enhancements

* We have expanded the data that Puppet Enterprise collects from PE installations to include basic information about agents and console usage. This data is sent to Puppet so we can improve our product. For details about what data is collected and how to opt out, see [Puppet Enterprise analytics data collection](./analytics_opt_out.html).

### Code Manager enhancements

* A new [`status` action](./code_mgr_cli.html#status) in the `puppet code` command verifies from the command line that Code Manager and file sync are responding.

* Code Manager now provides environment isolation for your resource types. Generated metadata files ensure that each environment uses the correct version of the resource type. For more information, see [Code Manager](./code_mgr.html#environment-isolation-metadata) documentation.

### Console enhancements

* Corrective change reporting has been added to [the node graph](./CM_graph.html) and [the Events page](./CM_events.html) in the PE console. PE differentiates between changes driven by updates to Puppet code (“intentional changes”) and changes made by Puppet to return a system to the desired state as defined by Puppet code (“corrective changes”). Corrective change reporting is available only for Puppet agents running PE 2016.4 or later.

* The node graph and Events page now provide information regarding whether a Puppet run was completed in enforcement or no-op mode, and whether changes were enforced or simulated. No-op mode reporting is available only for Puppet agents running PE 2016.4 or later.

### Service restart/reload enhancements

Most of PE's services (including pe-puppetserver, pe-puppetdb, pe-console-services, and pe-orchestration-services) now have a `reload` action, which acts like a restart but is significantly faster. If you need to refresh a service after changing its configuration, you can almost always reload it instead of restarting.

To reload a service, run `service <NAME> reload` instead of `service <NAME> restart`.

The `reload` action restarts a service _without_ restarting its underlying Java Virtual Machine (JVM) process. Since starting the JVM is the most time-consuming part of a restart, the speed improvement is very noticeable.

However, some configuration changes require a full restart. These are:

   * [Changes to Java arguments](./config_java_args.html), like heap size.
   * Changes to Puppet Server's [`ca.cfg`]({{puppetserver}}/configuration.markdown#service-bootstrapping) file, or anything else in a `services.d` directory.

All other config changes are reload-safe.

### Security enhancements

- Use the [`certregen` module](https://forge.puppet.com/puppetlabs/certregen), available on the Puppet Forge, to regenerate and redistribute Puppet CA certificates that are expiring soon, as the Puppet CA cert expires after five years. Refer to the module's README for full instructions.

### Monitoring and reporting enhancements

* A new `puppet infrastructure status` command displays errors and alerts from PE services, including the activity, classifier, and RBAC services, Puppet Server, and PuppetDB.

### Razor enhancements

* Razor now includes a [supported task](./razor_tasks.html#supported-tasks) for installing Fedora 23.
* Razor now supports Windows installation using international ISOs. When you [create a Windows policy](razor_windows_install.html#create-a-policy) to provision non-English systems, use the `node_metadata` attribute to specify a locale.
* New commands let you update an existing policy's [repository](./razor_client_commands.html#update-policy-repo), [broker](./razor_client_commands.html#update-policy-broker), or [node metadata](./razor_client_commands.html#update-policy-node-metadata). For example, without re-creating any policies, you can add a broker to a policy that used the `noop` broker, or switch to the `puppet-pe` broker when you upgrade to Puppet Enterprise.
* In the [`create-broker`](./razor_client_commands.html#create-broker) command, a new `ntpdate_server` property in the `configuration` attribute lets you specify an NTP server. The server is used to synchronize the date and time before installing the agent, which prevents certificate errors.
* In the [`modify-node-metadata`](./razor_client_commands.html#modify-node-metadata) command, a new `force` attribute lets you bypass errors in a batch operation with `no_replace`. Existing keys aren't modified.

## Deprecations and removals in PE 2016.5.1

### Support ends for some Puppet Enterprise versions

Puppet is [ending support for the following Puppet Enterprise versions](./overview_getting_support.html#puppet-enterprise-support-life-cycle) on **31 December 2016**:

* 3.8
* 2015.2
* 2015.3
* 2016.1

Puppet Enterprise 2016.2 will no longer be supported after **30 April 2017**.

If you're still using one of these versions, you must move to a supported version, preferably the most recent release.

* From PE 3.8,  [migrate to 2016.5]({{pe}}/migrate_monolithic.html).
* From PE 2015.2, 2015.3, 2016.1, or 2016.2, [upgrade to a version on the 2016.4 line](./pe/2016.4/upgrade_mono.html). This is our long-term support version of PE. From this version, you can then [upgrade to PE 2016.5]({{pe}}/upgrade_mono.html) if you want to take advantage of new features in this release.

### Platforms reaching end of support

Support soon ends for some platforms, as described in the [system requirements](./sys_req_os.html#upcoming-platform-end-of-life-eol).

## Related release notes

### Puppet release notes

This version of PE includes Puppet version 4.8.1. Refer to the [Puppet release notes]({{puppet}}/release_notes.html) for more information.

### Puppet agent release notes

This version of PE includes Puppet agent version 1.8.2. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html#puppet-agent-180) for more information.

### PuppetDB release notes

This version of PE includes PuppetDB version 4.2.5. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

### Puppet Server release notes

This version of PE includes Puppet Server version 2.7.2. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.























