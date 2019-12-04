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

## New features in PE 2016.4.15

PE 2016.4.15 provides [security improvements](/security).

## New features in PE 2016.4.14

PE 2016.4.14 provides [security improvements](/security).

## New features in PE 2016.4.13

PE 2016.4.13 provides [bug fixes](./release_notes_resolved_issues.html) and [security improvements](/security).

## New features in PE 2016.4.11

PE 2016.4.11 provides [security improvements](/security) and [enhancements](#enhancements-in-pe-2016411).

## Enhancements in PE 2016.4.11

### Specify proxy when using the install script

On Enterprise Linux systems, if you have a proxy between the agent and the master, you can now use the install script to specify an `http_proxy_host` to be used during package installation, for example `-s agent:http_proxy_host=<PROXY_FQDN>`. Previously, specifying a proxy host using the install script added the setting to `puppet.conf` without using it for installation. <!--PE-23034-->

### Unused data is cleaned up during installation or upgrade

When installing the agent on Solaris and AIX systems, the installer now cleans up binaries copied into the `/tmp` folder, improving system performance in some cases. <!--PE-23471-->

### SSL cipher suites added to the PE module

SSL cipher suites for Puppet Enterprise services are now managed through the `puppet_enterprise` module. The `puppet_enterprise::ssl_cipher_suites` parameter sets the SSL cipher suites for core Puppet services, including the port used, 8140. This parameter expects an array of SSL ciphers, for example:

`puppet_enterprise::ssl_cipher_suites: ['ECDHE-ECDSA-AES256-GCM-SHA384', 'ECDHE-RSA-AES256-GCM-SHA384', 'ECDHE-ECDSA-CHACHA20-POLY1305']`

Console SSL ciphers are managed separately through the `puppet_enterprise::profile::console::proxy::ssl_ciphers` parameter.

Cipher names are in RFC format. <!--PE-23011-->

### MCollective service agent updated to 3.1.5

The `mcollective-service-agent` has been updated to 3.1.5 to resolve bugs related to managing services on Solaris and with systemd. <!--PE-24036-->

### Options added to support script

The support script now includes options to encrypt the resulting tarball (`--encrypt`) and specify the number of days of logs to collect (`--log-age`). <!--PE-15428--> <!--PE-24023-->

## New features in PE 2016.4.10

PE 2016.4.10 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-2016410).

## Enhancements in PE 2016.4.10

### New Puppet agent platforms

This release adds support for the Puppet agent on:

* macOS 10.13 "High Sierra"
* RHEL 7 AArch64 architecture

### Specify PuppetDB blacklisted facts

Using the `facts_blacklist` setting in Hiera, you can now specify a list of facts that should not be stored in the PuppetDB database. See [Configure blacklisted facts](./config_puppetdb.html#configure-blacklisted-facts) for details.

## New features in PE 2016.4.9

PE 2016.4.9 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201649).

## Enhancements in PE 2016.4.9

### Control the state of the Puppet service during agent install

You can now control the state of the Puppet service when you install *nix or Windows agents with an install script. This capability enables manually kicking off the initial Puppet run or doing so with a provisioning system.

Use these flags to control the Puppet service:

| Option         | *nix                       | Windows                       | Values |
|----------------|----------------------------|-------------------------------|---------|
| ensure  | `--puppet-service-ensure <VALUE>` | `-PuppetServiceEnsure <VALUE>` | running, stopped |
| enable | `--puppet-service-enable <VALUE>` | `-PuppetServiceEnable <VALUE>` | true, false, manual, mask |

### MSI properties in Windows simplified agent install script

The simplified agent install script for Windows now supports setting certain MSI properties as flags in the PowerShell script. You can combine agent configurations with MSI properties.

| MSI property   | PowerShell flag |
|----------------|-----------------|
| `INSTALLDIR`   | `-InstallDir`     |
| `PUPPET_AGENT_ACCOUNT_USER` | `-PuppetAgentAccountUser` |
| `PUPPET_AGENT_ACCOUNT_PASSWORD` | `-PuppetAgentAccountPassword` |
| `PUPPET_AGENT_ACCOUNT_DOMAIN` | `-PuppetAgentAccountDomain` |

For example:

~~~
[Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; $webClient = New-Object System.Net.WebClient; $webClient.DownloadFile('https://<MASTER HOSTNAME>:8140/packages/current/install.ps1', 'install.ps1'); .\install.ps1 -PuppetAgentAccountUser "svcPuppet" -PuppetAgentAccountPassword "s3kr3t_P@ssword"
~~~

~~~
[Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; $webClient = New-Object System.Net.WebClient; $webClient.DownloadFile('https://<MASTER HOSTNAME>:8140/packages/current/install.ps1', 'install.ps1'); .\install.ps1 -PuppetAgentAccountUser "svcPuppet" -PuppetAgentAccountPassword "s3kr3t_P@ssword" agent:splay=true agent:environment=development
~~~

### New Puppet agent platforms

This release adds support for the Puppet agent on:

* SUSE Linux Enterprise Server 12 on POWER architectures
* Fedora 26


## New features in PE 2016.4.8

PE 2016.4.8 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201648).

## Enhancements in PE 2016.4.8

### Support for environment isolation

[Environment isolation]({{puppet}}/environment_isolation.html#environment-isolation) prevents resource types from leaking between your various environments. <!--PE-21213-->

### Windows agent installation secured with manually-transferred cert

You can now securely install Windows agents by [installing using a certificate](./install_windows.html#installing-using-a-certificate). <!--PE-21368-->

### New Puppet agent platforms

This release adds support for the Puppet agent on Debian 9 (Stretch).


## New features in PE 2016.4.7

PE 2016.4.7 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201647).

## Enhancements in PE 2016.4.7

### Secured database connections

Connections to PE databases can now be made only with certificates. Usernames and passwords are no longer allowed by default.  <!--PE-20992-->

### Secured agent installation with a manually transferred certificate

On Redhat, Ubuntu, SLES, Solaris, and AIX platforms, if you manually transfer CA certificates to agents and [install using the `--cacert` flag](./install_agents.html#install-agents-without-curl--k) to point to the master CA, subsequent downloads invoked by the installation script are now secured. <!--PE-21277-->

### New Puppet agent platforms

PE now supports Puppet agents on the following new platforms:

* Ubuntu 16.04 (ppc64le)
* Enterprise Linux 7 (ppc64le)

## New features in PE 2016.4.6

PE 2016.4.6 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201646).

## Enhancements in PE 2016.4.6

### Classifier node-check-in purge optimization

The PE classifier node-check-in purge has been optimized for speed and performance.

## Deprecations and removals in PE 2016.4.6

The `pe_console_prune` module was removed. This obsolete module was used to trim console reports in versions earlier than 3.8.

## New features in PE 2016.4.5

PE 2016.4.5 [upgrades the Puppet agent](#upgrade-to-puppet-agent-1101) and provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201645).

### Upgrade to Puppet agent 1.10.1

This release upgrades the Puppet agent to version 1.10.1. This upgrade provides critical fixes that couldn't be backported to the 1.7.x agent series and enables a smoother migration to the next planned major series, the Puppet 5 Platform (more details [here](https://puppet.com/blog/full-visibility-and-control-of-your-infrastructure-new-puppet-releases)). The update is backwards-compatible with previous 2016.4 releases and hasn't resulted in any known issues or changed behavior. This is the last version bump for the Puppet agent in the PE 2016.4 LTS release; future releases will use the Puppet agent 1.10.x series.

## Enhancements in PE 2016.4.5

### Compile masters retrieve agent packages from master of masters

Previously, compile masters downloaded agent packages from Puppet.com to make them available for agent installs, meaning they had to reach the internet to retrieve those packages. Compile masters now retrieve agent packages directly from the master of masters (MoM).

### Configure PXP agent log file location

The PXP agent logfile can be used to debug issues with the Puppet orchestrator. You can [change this location](./config_orchestration.html#configuring-pxp-agent-logfile-location) from the default as needed.

### Modifying `heap_mb` also modifies `memoryUsage limit`

Previously, modifying `heap_mb` setting, as documented in configuring [ActiveMQ heap usage](./config_java_args.html#activemq-heap-usage-puppet-master-only), did not modify the `memoryUsage limit` setting for the ActiveMQ broker. Increasing the `heap_mb` value now correspondingly increases the `memoryUsage limit` to 70 percent of the `heap_mb` value.

### Java garbage collection logging enabled by default

Java garbage collection (GC) logs can be useful when diagnosing performance issues with JVM-based PE services. GC logs are now enabled by default in PE, and the results are captured in the support script, but you can [disable them](./config_java_args.html#disable-java-garbage-collection-logging) if you need to.

### Support for AIX 7.2

This release adds support for running Puppet agents on AIX 7.2.

### MCollective policy action security enhancement

The MCollective package agent plugin provides the ability to install packages from any source (including a URL) and does not require that the packages are signed. This provides a `peadmin` user the ability to execute arbitrary code on any MCollective server.

   A default action policy has been put into place in PE that disallows using the package `install`, `uninstall`, and `purge` actions. The policy can be modified—and additional action policies can be added—using the `puppet_enterprise::profile::agent::allowed_actions` parameter to specify agent plugins you want to apply an action policy to, and a list of the actions you want to explicitly allow.

### MCollective client log level configurable

To help with troubleshooting, you can customize the MCollective client logging level either in the console or in `pe.conf` by setting `puppet_enterprise::profile::mcollective::peadmin::mco_loglevel` to `debug`, `warning`, or `error` instead of the default `info`.

### MCollective client keys security fix

MCollective client keys are now labeled sensitive and will not be stored in PuppetDB.

## Deprecations and removals in PE 2016.4.5

The following platforms have reached end of life (EOL):

* RHEL 4
* Fedora 23
* Mac OS X 10.9
* Ubuntu 15.10
* Ubuntu 10.04
* SLES 10

Refer to the system requirements for [a list of platforms that will soon be EOL](./sys_req_os.html#upcoming-platform-end-of-life-eol).

## New features in PE 2016.4.3

PE 2016.4.3 is primarily a release to fix bugs and provide security improvements. For more information, review the [resolved issues](./release_notes_resolved_issues.html) and [security vulnerabilities](/security) documentation.

### Support for macOS Sierra

This release adds support for running Puppet agents on macOS Sierra.

## Deprecations and removals in PE 2016.4.3

* We've removed the choice to perform a split install with the web-based installer. If you need to perform a split installation, [use the text-mode option](./install_text_mode_split.html).

* Refer to the system requirements for [a list of platforms that will soon be end-of-life (EOL)](./sys_req_os.html#upcoming-platform-end-of-life-eol).

* Refer to the [Puppet Enterprise support life cycle](./overview_getting_support.html#puppet-enterprise-support-life-cycle) for a list of support dates for our latest versions.

## New features in PE 2016.4.2

PE 2016.4.2 is primarily a release to fix bugs and provide security improvements. For more information, review the [resolved issues](./release_notes_resolved_issues.html) and [security vulnerabilities](/security) documentation.

## Deprecations and removals in PE 2016.4.2

* Refer to the system requirements for [a list of platforms that will soon be end-of-life (EOL)](./sys_req_os.html#upcoming-platform-end-of-life-eol).

## New features in PE 2016.4

### PE 2016.4 is long-term support release

PE 2016.4 is the next consecutive release following PE 2016.2. PE 2016.4 has been designated as a long-term support release for customers who require longer testing periods between PE upgrades. PE 2016.4 will be fully supported (security releases, critical bug fixes, and full customer support) for 24 months from its general availability date.

### Corrective change reporting in the PE console

This release introduces corrective change reporting in the PE console. PE now differentiates between changes driven by updates to Puppet code ("intentional changes") and changes made by Puppet to return a system to the desired state as defined by Puppet code ("corrective changes"). Use [the console's Overview page](./CM_overview.html#working-with-the-overview-page) to quickly identify nodes that received corrective changes on the last Puppet run, and view details of corrective change events on [the Reports page](./CM_reports.html). Corrective change reporting is available only for Puppet agents running PE 2016.4.

### Ability to opt out of Puppet data collection

Puppet Enterprise is automatically set to collect data from PE installations and send it to Puppet so we can improve our product. If you want to opt out of sending this data to Puppet, you can. For information about what kind of data is collected and how to opt out, see [Opting out of Puppet Enterprise analytics](./analytics_opt_out.html).

### No-op mode run statuses in the PE console

Reporting in the PE console provides more granularity and context about no-op mode run performance. The Overview page now offers a [breakdown of the run status](./CM_overview.html#node-run-statuses) of each node run in no-op mode on the last Puppet run. Filtering by no-op mode run status has also been added to [the Reports page](./CM_reports.html). No-op mode reporting is available only for Puppet agents that are running PE 2016.4.

## Enhancements in PE 2016.4

### Puppet orchestrator enhancements

- You can now directly enforce change to selections of your infrastructure with the Puppet orchestrator. This version of PE adds the ability to run orchestrator jobs against the following targets:

   - Nodes derived from a Puppet Query Language (PQL) query.
   - A specific node or list of nodes.
   - A single instance or all instances of an application in an environment.

- Puppet Application Orchestration and the orchestration service are now enabled by default in PE.

### Platform enhancements

* When installing PE agents on *nix-based systems, you can pass parameters to the install script to specify configuration settings for inclusion in `custom_attributes` and `extension_requests` sections of `csr_attributes.yaml`. See [Passing configuration parameters to the install script](./install_agents.html#passing-configuration-parameters-to-the-install-script) for an example.

* Windows agents can now be installed [using PE package management](./install_windows.html#installing-with-pe-package-management). You can also [pass configuration parameters to the install script for Windows agents](./install_windows.html#passing-configuration-parameters-to-the-install-script). Note that the command for installing agents with PE package management on Windows is different than the command used on *nix systems.

* The MCollective metadata refresh cron job has been improved so that when it's created, it's created with a random minute. Previously the cron job was created with a fixed runtime of 0, 15, 30, or 45 minutes. Now the load of running the job is spread evenly across time. If you are upgrading from a previous version of PE you can expect to see most agents change this cron resource. <!--PE-16315-->

### Console enhancements

* On the **Nodes** > **Classification** page, a hierarchical view replaces the previous alphabetical list.

* Timestamps in the console can be [displayed in local time](./console_accessing.html#changing-console-timestamps-to-local-time), with UTC time shown on hover.

* The Overview page provides a count of nodes on which one or more resources were enforced during the last Puppet run in no-op mode. Enforced resources are created when the `noop => false` metaparameter setting is used on no-op mode runs.

### Code Manager and r10k enhancements

* We've improved support for managing non-module content, such as Hiera data, with Code Manager and r10k. The Puppetfile includes an `install_path` option you can set for any Git repository you declare. This allows you to declare non-module content in your Puppetfile, and then use the `install_path` option to install it in your environment outside of the "modules" directory.

* This release adds the ability to track a control repo branch relative to the environment your Puppetfile is in. This means that when you create new branches from an existing branch of your control repo, you don't have to edit the new Puppetfile as extensively.

* The `puppet code` command now supports Windows and OSX systems. See [`puppet code`](./code_mgr_cli.html) documentation for usage details.

* For r10k, you can specify the level at which r10k purges unmanaged content during deployment. See the [`purge_levels`](./r10k_custom.html#purgelevels) parameter for details. <!--RK-246-->

### PE client tools enhancements

* You can now run PE client tools on Windows and OS X. See the [client tools installations instructions](./install_pe_client_tools.html#non-managed-workstation-installation) for more information.

### API enhancements

* When generating an authentication token, you can [affix a plain-text, user-specific label](./rbac_token_auth.html#setting-a-token-specific-label) to the token. Use this label to more readily refer to the token when working with RBAC API endpoints, or when revoking your own token.

* The DELETE /roles/&lt;rid&gt; endpoint in the v1 RBAC service API no longer requires the use of the `Content-Type` header.

## Deprecations and removals in PE 2016.4

* In this release, we've removed the pe-internal-* certificates for the classifier and orchestrator services. These services now use the agent certificate. MCollective continues to use the pe-internal-* certificates.

* Previously, in the Puppet orchestrator, you could pass an application target without a flag. That behavior is deprecated in this release. The `puppet job` subcommands now require a flag to run a job on a target. In addition, the `--env` flag is deprecated. Instead, use `--environment` or `--e`.

* This release deprecates the `puppet_enterprise::use_application_services` and the `puppet_enterprise::profile::orchestrator::use_application_services` parameters. PE ignores these parameters and raises a warning if you have set them. To avoid these warnings, remove the parameters from classification. If you need to disable these services, see [Disabling application management](./config_orchestration.html#disabling-application-management) and [Disabling orchestration services](./config_orchestration.html#disabling-orchestration-services).

* The PE client tools package no longer contains the Razor client. See the Razor installation documentation for instructions on [installing the Razor client](./razor_install.html#install-the-razor-client).

* The `puppet_enterprise::profile::master::metrics_enabled` parameter is no longer necessary and has been deprecated. If you set it, PE will notify you of the setting's deprecation.

* Refer to the system requirements for [a list of platforms that will soon be end-of-life (EOL)](./sys_req_os.html#upcoming-platform-end-of-life-eol).

* Refer to the [Puppet Enterprise support life cycle](./overview_getting_support.html#puppet-enterprise-support-life-cycle) for a list of support dates for our latest versions.

## Related release notes

### Puppet release notes

This version of PE includes Puppet version 4.10.12. Refer to the [Puppet release notes]({{puppet}}/release_notes.html) for more information.

### Puppet agent release notes

This version of PE includes Puppet agent version 1.10.14. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html#puppet-agent-11014) for more information.

### PuppetDB release notes

This version of PE includes PuppetDB version 4.2.3. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

### Puppet Server release notes

This version of PE includes Puppet Server version 2.6.1. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.
