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

## New features in PE 2017.2.5

PE 2017.2.5 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201725).

## Enhancements in PE 2017.2.5

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

* SUSE Enterprise Linux Server 12 on POWER architectures
* Fedora 26



## New features in PE 2017.2.4

PE 2017.2.4 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201724).

## Enhancements in PE 2017.2.4

### Purge nodes without a restart

You can now purge nodes without running Puppet on your master and reloading it. However, if you use compile masters, you must still run Puppet on all compile masters in order to revoke a node's certificate and have the change take effect.

### Windows agent installation secured with manually-transferred cert

You can now securely install Windows agents by [installing using a certificate](./install_windows.html#installing-using-a-certificate). <!--PE-21368-->

### New Puppet agent platforms

This release adds support for the Puppet agent on Debian 9 (Stretch).


## New features in PE 2017.2.3

PE 2017.2.3 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201723).

## Enhancements in PE 2017.2.3

### Secured database connections

Connections to PE databases can now be made only with certificates. Usernames and passwords are no longer allowed by default.  <!--PE-20992-->

### PuppetDB enhancements

PuppetDB now uses 14 days for a default “time-to-live” value (`node-purge-ttl`) before it deletes nodes that have been deactivated or expired. This default can be [changed as needed](./config_puppetdb.html#configure-node-purge-ttl).

### New Puppet agent platforms

PE now supports Puppet agents on the following new platforms:

* Ubuntu 16.04 (ppc64le)
* Enterprise Linux 7 (ppc64le)
* Amazon Linux 2017.03 (using packages for RHEL 6)

## New features in PE 2017.2.2

PE 2017.2.2 provides [bug fixes](./release_notes_resolved_issues.html), [security improvements](/security), and [enhancements](#enhancements-in-pe-201722).

## Enhancements in PE 2017.2.2

### Secured agent installation with a manually transferred certificate

On Redhat, Ubuntu, SLES, Solaris, and AIX platforms, if you manually transfer CA certificates to agents and [install using the `--cacert` flag](./install_agents.html#install-agents-without-curl--k) to point to the master CA, subsequent downloads invoked by the installation script are now secured.

### Classifier node-check-in purge optimization

The PE classifier node-check-in purge has been optimized for speed and performance.

### Garbage collection of completed code deployments

We've added a new Code Manager parameter, `deploy-ttl`. This parameter specifies the length of time completed deployments are retained before garbage collection, which is important to ensuring consistent Code Manager performance over time.

## New features in PE 2017.2

### Orchestrator in the console

You can now set up orchestrator jobs in the console. You can create node lists, either static or using Puppet Query Language, on which to run Puppet.

With orchestrator integrated into the console, you can set up jobs with ease, and use the console's reporting and infrastructure monitoring tools to review jobs and dig deeper into node run results.

See [Running jobs in the console](./orchestrator_job_run_console.html) for details.

### Packages inventory in the console

View an inventory of all packages installed on your nodes, and learn which nodes are using each package version. Use this data when determining which nodes are impacted by packages eligible for maintenance updates, security patches, and license renewals. Package inventory reporting is available for all nodes with a Puppet agent installed, including systems that are not actively managed by Puppet.

Package data collection is turned off by default. To turn on package data collection and use this feature, see [Viewing all packages in use](./CM_packages.html).

### PE is now available in Japanese

As part of our ongoing commitment to PE users in Japan, PE 2017.2 features Version 1 of Puppet Enterprise (Japanese).

Version 1 of Puppet Enterprise (Japanese) includes a Japanese GUI, and localization of the following services and resources into Japanese.

* Messages and responses in the Classifier, Role-Based Access Control (RBAC), Activity, and Orchestrator services. These messages are displayed when you query the API endpoints for each service.
* Instructions and messages in the PE web-based installer.
* Messages displayed when using the Orchestrator command line tool.
* The [Puppet Forge](https://forge.puppet.com) home page.
* [The Beginner's Guide to Modules](https://docs.puppet.com/ja/puppet/4.9/bgtm.html).
* [The Puppet Language Style Guide](https://docs.puppet.com/ja/puppet/4.9/style_guide.html).
* Module READMEs and descriptions for seven Puppet-supported modules (NTP, SQL Server, Stdlib, AWS, Tomcat, MySQL, and Tagmail). View the Japanese READMEs on the Puppet Forge by setting your browser language preference to Japanese. You can also find them in your modules directory. The default location is ./readmes/README_ja_JP.md.
* User documentation for [Roles and profiles]({{pe_ja}}/r_n_p_intro.html), [PE overview pages]({{pe_ja}}/pe_architecture_overview.html), and [PE release notes]({{pe_ja}}/release_notes.html).

Puppet Enterprise (Japanese) is included in the same tarball as the English version of PE. To view the PE installer, console, and the Puppet Forge in Japanese, set your web browser language preference to Japanese. To view API messages and command line tool messages in Japanese, set your system locale preference to Japanese. If you already have your browser and system preferences set to Japanese, the Japanese strings are displayed automatically.

We have also improved UTF-8 character encoding support in PE and in the Puppet components and services that are used with PE.

## Enhancements in PE 2017.2

### Console and console services enhancements

* The console now redirects to HTTPS when you attempt to connect over HTTP. The pe-nginx webserver now listens on port 80 by default.

   You can [disable the HTTPS redirect](./console_config.html#disable-https-redirect) in Hiera.

* Previously, the node classifier service stored a check-in for each node when its classification was requested. The check-in included an explanation of how the node matched the rule of every group it was classified into. This functionality created performance issues when managing a large deployment of nodes. The check-in storage is still available, but it's now disabled (`false`) by default.

   You can enable this by setting `puppet_enterprise::profile::console::classifier_node_check_in_storage` to `true` in the [console](./console_config.html).

* In this release, you can determine the amount of time that should pass after a node sends its last report before it is considered unresponsive. Set an integer to specify the value in seconds. The default is 3600 seconds (one hour).

   Adjust `puppet_enterprise::console_services::no_longer_reporting_cutoff` in the [console](./console_config.html).

* The `ping_interval` setting controls how long PXP agents will ping PCP brokers. If the agents don't receive responses, they will attempt to reconnect. The default is 120 seconds (two minutes).

   Adjust `puppet_enterprise::pxp_agent::ping_interval` in the [console](./config_orchestration.html).

* We've redesigned the console's navigation pane, and reduced its width by half.

* Quickly access the run report associated with a particular event by using the **View run report** link that now appears on the **Events** detail page.

* The fact value filters on the **Overview** and **Reports** pages now display warning messages if you attempt to use an invalid regular expression, invalid string operator, or empty fact name.

### Puppet orchestrator enhancements

* The Puppet orchestrator communicates with PCP brokers on compile masters on port 8143 and sends job-related messages to the brokers, which are then relayed by the brokers to PXP agents. As you add compile masters, you’re able to scale the number of PCP brokers that can send orchestration messages to agents. See [Configure compile masters for orchestration scale](./install_multimaster.html#configure-compile-masters-for-orchestration-scale) for instructions.

  In High Availability installations, you can now configure PXP agents to communicate with compile masters, instead of just the master or replica, using the new `pcp_broker_list` parameter.

* Use the PXP agent log file to debug issues with the Puppet orchestrator. You can [change its location](./config_orchestration.html#configuring-pxp-agent-logfile-location) from the default as needed.

* The `pe-puppetserver` service now defaults to an open file limit of `12000` to support orchestrator scale with PCP brokers.

### Code Manager enhancements

* We've added a new flag, [`--dry-run`](./code_mgr_cli.html#puppet-code-actions), to the `puppet-code` command. When you run `puppet-code` with this flag, it tests connections to your control repos and returns a consolidated list of all environments in the control repos.

* The behavior of the `--wait` flag used with the `puppet-code` command has been updated to improve accuracy and completeness of reporting. Previously, `--wait` returned results after deploying code to the code-staging directory. The flag now waits for file sync to also deploy code to the live code directory on all compile masters before returning results.

   Due to this updated behavior, running `puppet-code deploy` with `--wait` takes a minimum of 10 seconds longer than in previous PE versions. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes.

### Analytics enhancements

* Puppet Enterprise collects data about your PE installation and sends it to Puppet so we can improve our product. In addition to previously collected analytics, we now also collects basic information about:

  * Puppet Server performance
  * Cloud platform and hypervisor use
  * All-in-one `puppet-agent` package version
  * Use of MCollective and non-default user roles
  * JVM memory usage
  * Certificate autosign setting

  For details about what data we collect and how to opt out, see [Puppet Enterprise analytics data collection](./analytics_opt_out.html).

### Security enhancements

* For those with security compliance needs, PE now supports [disabling TLSv1](./disable_tlsv1.html). Services in PE support TLS versions 1, 1.1, and 1.2.

* The MCollective package agent plug-in helps you install packages from any source (including a URL) and does not require that the packages are signed. This provides a `peadmin` user the ability to execute arbitrary code on any MCollective server.

   A default action policy has been put into place in PE that disallows using the package `install`, `uninstall`, and `purge` actions. The policy can be modified and additional action policies can be added using the `puppet_enterprise::profile::mcollective::agent::allowed_actions` parameter to specify agent plug-ins you want to apply an action policy to, and a list of the actions you want to explicitly allow.

* MCollective client keys are now labeled sensitive and will not be stored in PuppetDB.

### Razor enhancements

* This version adds support for the Razor client on Windows 2016 Servers and adds new Puppet-supported tasks for SLES 11 and 12 and Windows 2016.

### Other enhancements

* Previously, compile masters downloaded agent packages from puppet.com to make them available for agent installs, meaning they had to reach the internet to retrieve those packages. Compile masters now retrieve agent packages directly from the master of masters.

* Java garbage collection logs can help you diagnose performance issues with JVM-based PE services. Garbage collection logs are now enabled by default in PE, and the results are captured in the support script, but you can [disable them](./config_java_args.html#disable-java-garbage-collection-logging) if you need to.

* To help with troubleshooting, you can customize the MCollective client logging level either in the console or in `pe.conf` by setting `puppet_enterprise::profile::mcollective::peadmin::mco_loglevel` to `debug`, `warning`, or `error` instead of the default `info`.

## Deprecations and removals in PE 2017.2

* We've removed the previously unsupported option to disable file sync while Code Manager remains enabled.

* This release deprecates the `file_sync_repo_id` and `file_sync_auto_commit` Code Manager parameters. PE ignores these parameters and raises a warning if you have set them. <!--CODEMGMT-1020, PE-18299-->

### Platforms reaching end of support

* RHEL 4, Fedora 23, and Ubuntu 12.04 have reached end-of-life (EOL).

* Refer to the system requirements for [a list of platforms that will soon be EOL](./sys_req_os.html#upcoming-platform-end-of-life-eol).

* Refer to the [Puppet Enterprise support life cycle](./overview_getting_support.html#puppet-enterprise-support-life-cycle) for a list of support dates for our latest versions.

## Related release notes

### Puppet release notes

This version of PE includes Puppet version 4.10.8. Refer to the [Puppet release notes]({{puppet}}/release_notes.html) for more information.

### Puppet agent release notes

This version of PE includes Puppet agent version 1.10.8. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html#puppet-agent-1108) for more information.

### PuppetDB release notes

This version of PE includes PuppetDB version 4.4.2. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

### Puppet Server release notes

This version of PE includes Puppet Server version 2.7.3. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.























