---
layout: default
title: "Security and Bug Fixes"
canonical: "/pe/latest/release_notes_security.html"
---

This page contains information about security fixes in Puppet Enterprise (PE) 2015.3. It also contains a select list of those bug fixes that we believe you'll want to learn about.

For more information about this release, also see the [Known Issues](./release_notes_known_issues.html) and [New Features](./release_notes.html).

> **Tip:** This PE release contains several components that have additional release notes. Refer to [Related release notes](#related-release-notes-for-this-version-of-pe) for more information.

## Puppet Enterprise 2015.3.3

### Security Fixes

#### CVE-2016-2786: Incorrect Client Verification in Puppet Communications Protocol

- Posted: March 14, 2016 
- Assessed Risk Level: High
- CVSS v3 Base Score: 9.6

The Puppet Communications Protocol included in early versions of the PE 2015.3 series did not properly validate certificates in all cases. This potentially allowed for arbitrary remote code execution on Puppet agent nodes.

In PE 2015.3.2 and earlier, the pxp-agent component did not properly validate the server certificate. This made it possible for an attacker to impersonate a broker and issue commands to the agent, assuming the attacker could force the agent to connect to an arbitrary broker via a secondary attack (DNS spoofing, etc).

Default configurations of the open source Puppet agent were not vulnerable.

Affected software versions:
Puppet Enterprise 2015.3.x prior to 2015.3.3.

This issue is fixed in 2015.3.3 and Puppet Agent 1.3.6.

### Puppet agent release notes

This version of PE includes Puppet agent version 1.3.6. Refer to the [Puppet agent release notes]({{puppet}}/release_notes_agent.html) for more information.

## Puppet Enterprise 2015.3.2

### Security Fixes

#### CVE-2016-0701: Fixed issues for January 2016 OpenSSL security announcement

- Posted February 9, 2016
- Assessed Risk Level: Medium

On January 28, 2016 OpenSSL announced several security vulnerabilities. Puppet agent, Puppet (Windows), and Puppet Enterprise include a vulnerable version of OpenSSL. OpenSSL has been updated to address CVE-2016-0701.

For more information, [see the OpenSSL announcement](https://www.openssl.org/news/secadv/20160128.txt).

This issue is fixed in Puppet Enterprise 2015.3.2.

#### CVE-2015-5254: Vulnerability in ActiveMQ web console

The default configuration of Puppet Enterprise is not affected by the December 2015 ActiveMQ security announcement. However, if you have enabled the ActiveMQ web console you may be vulnerable.

Puppet Enterprise 2015.3.2 includes an updated version of ActiveMQ to address this vulnerability.

For more information, see the [ActiveMQ security announcement](http://activemq.apache.org/security-advisories.data/CVE-2015-5254-announcement.txt).

This issue is fixed in Puppet Enterprise 2015.3.2.

#### CVE-2015-7551: Unsafe tainted string usage in Fiddle and DL

- Posted February 9, 2016
- Assessed Risk Level: Low

Puppet Enterprise 2015.3.2, includes an update to address this vulnerability.

For more information, see the [Ruby security announcement](https://www.ruby-lang.org/en/news/2015/12/16/unsafe-tainted-string-usage-in-fiddle-and-dl-cve-2015-7551).

This issue is fixed in Puppet Enterprise 2015.3.2.

#### Fixed issues for January 2016 Java security announcement

- Posted February 9, 2016
- Assessed Risk Level: Medium

Puppet Enterprise 2015.3.2 includes updates to address the Java security announcement.

For more information, see [the Oracle Java announcement](http://www.oracle.com/technetwork/topics/security/cpujan2016-2367955.html#AppendixJAVA).

This issue is fixed in Puppet Enterprise 2015.3.2.

### Bug Fixes

#### Fixed: Upgrading Puppet agents could leave two MCollective processes running

This release corrects an issue in which upgrades using the PE agent upgrade script could leave two MCollective processes running on the agent. This affected upgrades of RHEL 4, 5, and 6 Puppet agents from 2015.2.1 through 2015.3.1. 

This did not affect the Puppet master agent, the PE console agent, the PuppetDB agent, or any compile master agents.

#### Fixed: MCollective class filtering broken for upgrades from 3.8.x

Perviously, if you upgraded from PE 3.8.x, the Puppet agent's `classfile` setting in `puppet.conf` was not reset to the new default location for new (AIO) Puppet agents. This affected nodes in the PE core infrastructure.
 
#### Fixed: Installations with external PostgreSQL failed

On previous versions in the 2015.3 series, installations using an external PostgreSQL would fail.

#### Fixed: Setting the `r10k_proxy` parameter caused code manager to fail

This version corrects an issue where if you set the [`r10k_proxy`](./code_mgr_config.html#configure-after-pe-installation) parameter in the `puppet_enterprise::profile::master` class, Code Manager would fail with a `disallowed-key` error.

#### Fixed: `prefix` parameter did not work with code manager webhook

The [`prefix`](./code_mgr_webhook.html#query-parameters) query parameter of Code Manager's `/v1/webhook` endpoint was not working correctly. This parameter should now work correctly with the webhook.

#### Fixed: upgrades disabled file sync service

Before this fix, PE upgrades would disable the file sync service, even if you had enabled it on a previous version.

#### Fixed: Puppet jobs run occasionally would hang

In some rare cases, Puppet job runs didn't always stop when a job finished and the run would subsequently hang.

#### Fixed: Agents in Puppet job runs could request incorrect enviroment

When you use the Puppet Orchestrator `puppet job` command with the `environment` flag, Puppet agent runs participating in the job should request the specified environment. This release corrects an issue in which agents could request a catalog for a different environment than the one specified in the job.

#### Fixed: PE uninstaller did not remove `pe-orchestration-services` user

This fix corrects an issue in which the PE uninstaller script did not remove the `pe-orchestration-services` user. 

#### Fixed: Updating `agent_version` parameter didn't download new puppet-agent version

Previously, if you attempted to update the `agent_version` parameter of an existing `pe_repo::platform::` class, PE did not download and make available the new puppet-agent version for that platform. 

#### Fixed: Puppet runs used system OpenSSL instead of packaged version

This fix corrects an issue in which Puppet runs were using the system's package of OpenSSL instead of the version packaged with Puppet Enterprise. 

#### Fixed: Razor `delete-repo` command didn't remove the repository folder from the server

In previous releases, the `delete-repo` command removed the repository from the Razor database, but didn't delete downloaded files on disk. With this release, if you supplied the `iso_url` property when you created the repository, the repository folder is also deleted from the server.

#### Fixed: PuppetDB issue in which non-UTF8 data took too long to process

Only include debugging information for the first instance of non-UTF8 data within a command.


## Puppet Enterprise 2015.3.1

### Security Fixes

#### CVE-2015-7330: Non-whitelisted hosts could access Puppet communications protocol

* Posted December 29, 2015
* Assessed Risk Level: High
* CVSS v3 score: 8.8

Puppet Enterprise 2015.3 contained a misconfiguration in which non-whitelisted hosts were permitted to communicate over the Puppet communications protocol and potentially control Puppet.

This issue is fixed in 2015.3.1.

## Puppet Enterprise 2015.3

### Security Fixes

#### CVE-2915-8470 Puppet Enterprise Console JSESSIONID Cookies Are Issued With the Secure Flag

* Posted December 8, 2015
* Assessed Risk Level: Medium

In previous releases, the JSESSIONID cookie served by the PE console didn't have the Secure flag set. Although the PE console uses HTTPS by default, a remote attacker could cause a user to send the JSESSIONID cookie in plain text over an HTTP session, potentially allowing the session to be hijacked.

In PE 2015.3, JSESSIONID cookies set by the PE console have the Secure flag set by default.

This issue affects PE 3.7.x, 3.8.x, and 2015.2.x. This issue is fixed in PE 2015.3.

### Bug Fixes

#### Fixed: Puppet Agent Service Did Not Properly Stop When Upgrading Compile Masters on Ubuntu Platforms 

Previously, if you attempted to upgrade Ubuntu compile masters, you may have encountered a situation in which the Puppet agent service was not properly stopped when the new agent was installed (which is part of the upgrade process). When it was time for the agent service to start and run Puppet, an error was raised indicating a run was already in progress: `Notice: Run of Puppet configuration client already in progress; skipping (/var/opt/lib/pe-puppet/state/agent_catalog_run.lock exists)`.

This was due to the fact that the agent service didn't stop. This caused the upgrade to fail. 

#### Fixed: `max-requests-per-instance` Caused a Memory Leak

When using the optional `max-requests-per-instance` setting in `puppetserver.conf`, Puppet Server should flush the JRuby instance from memory and replace it with a fresh instance after serving a number of requests defined by that setting. This can be handy when dealing with memory leaks in module code.

However, the outgoing JRuby instances were not flushed as expected during garbage collection, resulting in Puppet Server causing a memory leak that could destabilize the server after a sufficient number of replacement cycles.

#### Fixed: User's IP Address is Logged When the User Attempts to Log In to the PE Console

In previous releases, the information logged during a PE console login attempt did not include the user's IP address. In PE 2015.3, the log information has been expanded to include the login user's IP address. The default location for the log is `/var/log/puppetlabs/console-services/console-services-access.log`.

To match the IP address to a user, you'll need to correlate the times in `console-services.log` and `console-services-access.log`.

#### Fixed: Large Amount of Classification No Longer Stalls Upgrades

This release corrects an upgrade issue in which the node classifier would become out of sync with the Puppet master due to large amounts of classification that needed to be updated. The installer/upgrader now waits for classification to be updated before proceeding. Additionally, you can add `q_classification_attempts=` to an answer file for the console node. Adding this question will change the amount of retries before the installer proceeds, which will give the node classifier more time to sync large amounts of classification data. The default number of attempts is 150.

#### Fixed: Pre-2015.2 MCollective Plugin Library Removed

If you upgraded to the PE 2015.2 series, we changed the MCollective plugin library path to `/opt/puppetlabs/mcollective/plugins` (`C:\ProgramData\PuppetLabs\mcollective\plugins` on Windows), but we did not remove the library from from the old path, `/opt/puppet/libexec/mcollective/plugins` (or `C:\ProgramData\PuppetLabs\mcollective\etc\plugins` on Windows). This version of PE removes the old plugin library. In addition, the old plugin path is deprecated and will likely be removed in future versions.

Note that we did not remove any custom MCollective plugins you have installed.

#### Fixed: Monolithic Upgrades Could Fail if pe-postgresql Service Not Running

In previous versions, upgrades of monolithic installs could fail if the pe-postgresql service was not running. The upgrade now ensures that the pe-postgresql service is running before starting an upgrade.

#### Fixed: Agent Installation Getting Agent Package for Wrong Location

Previously if you placed the puppet-agent package in your own repository, it may have been used instead of the repository on the Puppet master when when you ran the installation script. This fix ensures the script retrieves agent packages from the Puppet master.

#### Fixed: Empty Stings in Hiera Overrides and Puppet Enterprise Class Variables Caused Failures in Catalog Compilations

Empty strings in Hiera overrides or in variables in the Puppet Enterprise class (via changes in the console) could cause Puppet catalog compilations to fail.

#### Fixed: Querying the Nodes Endpoint of the Classifier Service Could Exhaust Memory

If a large number of nodes were returned when querying the `v1/nodes` endpoint of the classifier service API, the pe-console-services process could exhaust the memory and return a 500 error.

This issue has been addressed. Now, when querying the `v1/nodes` endpoint, you get the same list of nodes as before, but you only get the most recent check-in information for each node rather than all previous check-ins.

#### Fixed: Multiple $ Signs in a Parameter Could Not Be Escaped

There are two specific circumstances in which escaping a $ sign did not work as expected.

1. If you used $ signs to specify the same variable multiple times within a string, and you escaped at least one $ sign but not all of them, then the escaping did not work and all variables were interpolated. For example, assuming that your hostname is "centos7," `"My \$hostname is $hostname."` should be interpolated as `"My $hostname is centos7."`, but instead, it was interpolated as `"My centos7 is centos7."`.

2. If you used a $ sign to specify a variable and then immediately followed it by one or more escaped $ signs in the same string, such as `"$one\$two"`, both `one` and `two` were escaped, even though `one` should have been interpolated as a variable.

Both of these escaping issues have been fixed.

#### Fixed: HTTPS Secure Flag is Set For Session Cookies

In earlier releases, the secure flag was not set on session cookies. In PE 2015.3, the secure flag informs browsers that a cookie should only be sent on connections that are encrypted with HTTPS.

#### Fixed: Trusted Hash-Based Rules in the Classifier API Resulted in Unexpected Behavior

In PE 2015.2, if you used the Classifier API to create a node group, and added a rule that used a trusted hash, the matching nodes were displayed correctly in the console under **Nodes** > **Classification** > <NODE GROUP NAME> > **Matching nodes**. However, in **Nodes** > **Inventory**, if you clicked one of the matching nodes, and then went to the **Groups** tab, the node group was not listed. This bug has been fixed.

#### Fixed: Nodes Could Be Pinned an Infinite Number of Times

When pinning a node to a node group in earlier versions of the PE console, if you pinned the node multiple times in the **Rules** tab, the console did not resolve this to a single entry. This bug has been fixed so that the duplicate pinned nodes are resolved to a single entry upon refresh.

#### Fixed: External Directory Groups Could Not Be Imported When Using SSL Certificate Verification

If you [configured PE to verify directory server certificates](./rbac_ldap.html#verify-directory-server-certificates), PE would fail to import user groups. This was due to an incorrect value that RBAC attempted to pass to the directory server. This bug has been fixed. RBAC now passes the correct value to the directory server and you can successfully import user groups when PE is configured to verify the directory server certificate.

#### Fixed: Incorrect Events Count Displayed for Changes to File Resource Properties

The summary count of **Resources with changes** displayed the number of distinct nodes with events for this resource, rather than the number of actual events. This has been fixed.

#### Fixed: r10k Incorrectly Switched from Forge Modules to Git Modules

If a Git version of a module was installed via a Puppetfile, and then the Puppetfile entry was updated to use the Forge version of the module, r10k assumed that the Forge module was installed. Now r10k correctly replaces the Git version of a module with the Forge version.

#### Fixed: r10k Crashed on Unresolvable Rugged Ref

When r10k was using the rugged provider, checking out an unresolvable ref threw an error because r10k didn't always ensure that the ref was resolvable. This has been fixed so that r10k explicitly ensures that refs can be resolved before it checks them out.

#### Fixed: r10k Didn't Update cachedir Settings Properly

When r10k created a Git working repository, it permanently linked the repository to a cached Git repository via the Git alternates file, which could break Git repos. However, if the cachedir setting changed, existing repositories still referenced the old path. Now r10k always ensures that the alternates file is up to date before any Git operations can happen.

## Related release notes

### Puppet agent release notes

This version of PE includes Puppet agent version 1.3.6. Refer to the [Puppet agent release notes](/{{puppet}}/release_notes_agent.html) for more information.

### PuppetDB release notes

This version of PE includes PuppetDB version 3.2.4. Refer to the [PuppetDB release notes]({{puppetdb}}/release_notes.html) for more information.

### Puppet Server release notes

This version of PE includes Puppet Server version 2.2.1. Refer to the [Puppet server release notes]({{puppetserver}}/release_notes.html) for more information.

### Puppet 4.3 release notes

This version of PE includes Puppet version 4.3.2. Refer to the [Puppet release notes]({{puppet}}/reference/release_notes.html) for more information.

