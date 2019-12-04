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

## New features in PE 2017.1.1

PE 2017.1.1 is primarily a bug fix release. For more information, review the [resolved issues](./release_notes_resolved_issues.html).

## New features in PE 2017.1

### Hiera 5

Hiera 5 is a backward-compatible evolution of Hiera, incorporating everything we learned from the experimental Puppet lookup feature. Hiera 5 features include:

* True environment- and module-level data. Any environment or module can include its own `hiera.yaml` file and its own data sources.
    * Learn more: [How the three config layers work]({{puppet}}/hiera_layers.html) and [How hierarchies work]({{puppet}}/hiera_hierarchy.html).
* A new backend system that simplifies integrating custom data sources with Puppet.
    * Learn more: [How custom backends work]({{puppet}}/hiera_custom_backends.html).
* Improved performance.
* Better debugging, with complete explanations where Hiera looked and where it found values. To try it for yourself, run `puppet agent -t --debug` on a node, or run `puppet lookup <LOOKUP KEY> --environment <ENV> --explain`.
* A HOCON data backend, in addition to the existing YAML and JSON backends.

If you already use Hiera, see [Migrating existing data to Hiera 5]({{puppet}}/hiera_migrate.html) to learn how to start using these new features.

## Enhancements in PE 2017.1

### RBAC and activity service enhancements

* A user's activity log now records when they run Puppet from the orchestrator CLI or from the **Run Puppet** button in the console.

### Analytics enhancements

* Puppet Enterprise collects data about your PE installation and sends it to Puppet so we can improve our product. In addition to what it collected previously, it now also collects basic information about node groups, orchestrator jobs, and Amazon Web Services Marketplace Image use. For details about what data we collect and how to opt out, see [Puppet Enterprise analytics data collection](./analytics_opt_out.html).

### Console enhancements

* Previously, if you restarted the `pe-console-services` service, you would be logged out of your console session. In this release, you're no longer logged out when you restart this service.

* We have enhanced the usability of the node graph, including updates to controls on the details pane and dependency view.

### Platform enhancements

* This release adds support for Puppet agent on the following platforms:

   * AIX 7.2
   * Ubuntu 16.10 (Yakkety) ([See installation known issue](./release_notes_known_issues_install.html#new-ubuntu-1610-agents-cannot-be-installed-with-pe-package-management).)
   * Fedora 25

## Deprecations and removals in PE 2017.1

* We removed the following flags and configuration file location, which were deprecated in previous releases:

   - `puppet-job list` is now `puppet-job show`
   - `puppet-job --env` is now `puppet-job --environment`
   - `puppet-job App[inst]` is now `puppet-job -a App[inst]`
   - `~/.puppetlabs/etc/puppet/orchestrator.conf` is no longer a valid location. Instead use `~/.puppetlabs/client-tools/orchestrator.conf`.

### Platforms reaching end of support

* Support soon ends for some platforms, as described in the [system requirements](./sys_req_os.html#upcoming-platform-end-of-life-eol).

* Refer to the [Puppet Enterprise support life cycle](./overview_getting_support.html#puppet-enterprise-support-life-cycle) for a list of support dates for our latest versions.

## Related release notes

### Puppet release notes

This version of PE includes Puppet version 4.9.4. Refer to the [Puppet release notes]({{puppet}}/release_notes.html) for more information.

### Puppet agent release notes

This version of PE includes Puppet agent version 1.9.3. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html#puppet-agent-193) for more information.

### PuppetDB release notes

This version of PE includes PuppetDB version 4.3.2. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

### Puppet Server release notes

This version of PE includes Puppet Server version 2.7.2. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.























