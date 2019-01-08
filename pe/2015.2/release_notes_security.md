---
title: "PE 2015.2» Release Notes >> Security and Bug Fixes"
layout: default
subtitle: "Security Fixes"
canonical: "/pe/latest/release_notes_security.html"
---

This page contains information about security fixes in the Puppet Enterprise (PE) 2015.2 series. It also contains a select list of those bug fixes that we believe you'll want to learn about.

For more information about this release, also see the [Known Issues](./release_notes_known_issues.html) and [New Features](./release_notes.html).

## Puppet Enterprise 2015.2.3 (November 5, 2015)

### Security Fixes

#### Oracle Java October 2015 Security Fixes 

- Posted November 5, 2015
- Assessed Risk Level: Medium

On October 20th, Oracle announced several security vulnerabilities in Java. Puppet Enterprise versions prior to 2015.2.3 contained affected versions of Java. Puppet Enterprise 2015.2.3 contains updated Java packages that address the vulnerabilities. For more information about the Java vulnerabilities, refer to the [Oracle security announcement](http://www.oracle.com/technetwork/topics/security/cpuoct2015-2367953.html#AppendixJAVA).

This issue affects PE 2015.2.x.
This issue is fixed in PE 2015.2.3.

#### PostgreSQL October 2015 Security Fixes

- Posted November 5, 2015
- Assessed Risk Level: Low

In October, the PostgreSQL project announced several security vulnerabilities in the PostgreSQL database server. Puppet Enterprise versions prior to 2015.2.3 contained affected versions of PostgreSQL. Puppet Enterprise 2015.2.3 contains updated PostgreSQL packages that address the vulnerabilities. For more information about the PostgreSQL vulnerabilities, refer to the [PostgreSQL security announcement](http://www.postgresql.org/about/news/1615/).

This issue affects PE 2015.2.x.
This issue is fixed in PE 2015.2.3.

#### CVE-2015-7328 World-readable CA Key in Puppet Server

- Posted November 5, 2015
- Assessed Risk Level: Low

During the initial installation and configuration of Puppet Enterprise, there is a short window of time where the generated CA key is left world-readable. This is corrected later during the configuration/bootstrapping steps.

In Puppet Enterprise 2015.2.3, the CA key (and all other SSL private keys) are created with the correct permissions.

This issue affects PE 2015.2.x
This issue is fixed in PE 2015.2.3.

### Bug Fixes

#### Fixed: ActiveMQ Networks of Brokers Collapse at Scale

This release corrects and issue for environments with more than 1500 nodes in which ActiveMQ hub and spoke topologies did not properly scale. The fix was to switch from duplex to simples ActiveMQ network connectors. 

#### Fixed: The pe-postgresql Service Sometimes Failed to Start

In this release, we've corrected an issue in which the pe-postgresql service sometimes failed to start after system reboots. 

## Puppet Enterprise 2015.2.2 (October 13, 2015)

### Bug Fixes

#### Fixed: Agent Upgrades From 2015.2.0 to 2015.2.1 Disabled the `puppet` Service

This release provides a fix for an issue in which agent upgrades from PE 2015.2.0 to 2015.2.1 disabled the Puppet service. This affected agents running on RHEL 5 and 6 and SLES 11.

#### Fixed: You Could Not Add Host Entries to Client-Whitelist for Puppet Server `ca.conf`

In the previous version of PE, an issue prevented you from being able to add host entries to the client-whitelist for the Puppet Server `ca.conf` file, `/etc/puppetlabs/puppetserver/conf.d/ca.conf`, using the console or Hiera.

#### Fixed: Puppet Masters Upgraded to PE 2015.2.1 Had Outdated Agent Packages in pe_repo for Agent Installation Script

When Puppet masters were upgraded from PE 2015.2.0 to PE2015.2.1, they did not have the correct agent packages in pe_repo for agents being upgraded to PE 2015.2.1 via the agent installation script.

## Puppet Enterprise 2015.2.1 (September 24, 2015)

### Security Fixes

#### Fixed Cross-Site Scripting (XSS) Vulnerability in PE Console

- Posted: 9/24/2015
- Assessed Risk Level: Medium
- CVSS v2 Score: 4.9

This fix corrects an issue in which the PE console login redirect was vulnerable to reflected cross-site scripting. This could allow an attacker to craft a URL that would execute arbitrary JavaScript code. 

#### Fixed Arbitrary URL Redirection Vulnerability 

- Posted: 9/24/2015
- Assessed Risk Level: Medium
- CVSS v2 Score: 4.9

In PE 2015.2.0, the PE console could accept a redirect parameter to a third-party site in its authentication URL. This fix properly validates the URL and prevents redirection to a third-party site.

### Bug Fixes

#### http-client Did Not Close Properly When Signing Certificates

In PE 2015.2, when accepting or rejecting node certificate signing requests in the Inventory page in the PE console, the http-client was not properly closed. This could lead to a resource leak. This bug has been fixed.

#### Certname Field in Console Classification Did Not Filter Correctly

In PE 2015.2, when pinning a node to a group in the Classification page in the console, the list of nodes was not filtering correctly as you type a name. This bug has been fixed and the list now filters as you type.

#### Autofill Did Not Display Correctly When Editing a Pinned Node

In PE 2015.2, when editing the name of a pinned node in the Rules tab in the console, the autofill incorrectly provided "undefined" as an option. This bug has been fixed and valid certnames are not displayed in the autofill list.

#### A $ Sign in a Parameter Could Not Be Escaped

In earlier releases, a $ sign could not be properly escaped in a parameter value. This issue has been fixed.

#### r10k Did Not Download PE-Only Modules

In PE 2015.2, r10k failed to download PE-only modules. This issue is fixed in this release, and r10k should now download PE-only modules properly.

#### Installer Failed Due to SSL Errors with AmazonAWS 

In some cases when attempting to install PE 2015.2, some master platforms received SSL errors when attempting to connect to s3.amazonaws.com, and were unable retrieve puppet-agent packages needed for installation. In most cases, you could properly install after updating the CA cert bundle on the master platform. 

#### Debian-Based Platforms Raised Error About Non-Existent Files Mount

In previous versions of PE, for Debian-based platforms, `fileserver.conf` and `/etc/puppetlabs/puppet/files` were managed by the pe-puppetserver-common package. This package is no longer part of PE, and `/etc/puppetlabs/puppet/files` has been removed. 

In PE 2015.2, Puppet agent runs would raise an error that it could not mount files at `/etc/puppetlabs/puppet/files`. This has been fixed in PE 2015.2.1. 

#### Permissions on Compile Master Configuration Files Toggled on Puppet Runs

The user and group configuration files and directories on compile masters in the 2015.2 series were not being properly managed; Puppet toggled permissions between the root user and the puppet user on these files and directories during Puppet runs. This issue has been fixed in PE 2015.2.1.

#### Puppet Runs on Puppet Master Took Several Minutes When Behind Proxy Server

Due to a network timeout check, Puppet runs would take several minutes on PE 2015.2 Puppet masters that were behind proxy servers. This check has been removed, and this issue is resolved in PE 2015.2.1. 

#### RPC Puppetral MCollective Agents Were Broken in 2015.2

This releases fixes an issue that prevented RPC Puppetral MCollective agents from working in PE 2015.2.

#### 2015.2 Upgrades Failed If Echo in `bash_profile`

In PE 2015.2 it was possible for upgrades to fail if there was an echo in the `bash_profile` on the system where PE was being installed. This issue has been fixed in PE 2015.2.1. 

#### Puppet Master Could Not Be Removed and Re-Added to CA Group

In PE 2015.2, if you removed the Puppet master from the PE Certificate Authority group and later re-added it, Puppet runs would fail. This issue is fixed in 2015.2.1.

## Puppet Enterprise 2015.2

### Security Fixes

#### Oracle Java July 2015 Security Fixes

- Posted July 28, 2015
- Assessed Risk Level: Medium

On July 14th, Oracle announced several security vulnerabilities in Java. PE versions prior to 2015.2.0 contained a vulnerable version of Java. PE 2015.2.0 contains an updated version of Java that has patched the vulnerabilities. For more information about the Java vulnerabilities, refer to the [Oracle security announcement](http://www.oracle.com/technetwork/topics/security/cpujul2015-2367936.html#AppendixJAVA).

#### Vulnerabilities in curl

- Posted July 28, 2015
- Assessed Risk Level: Low

On June 17th, The open source curl project announced two security vulnerabilities in the libcurl http client library. The Puppet agent package prior to 1.2.2 contained a vulnerable version of curl. Puppet agent 1.2.2 contains an updated version of curl. For more information about the curl vulnerabilities, refer to [CVE-2015-3236](http://curl.haxx.se/docs/adv_20150617A.html) and [CVE-2015-3237](http://curl.haxx.se/docs/adv_20150617B.html).

#### Cross-site Scripting (XSS) Vulnerability in PE Console

- Posted July 28, 2015
- Assessed Risk Level: Medium

Parts of the PE console were found to be susceptible to clickjacking and CSRF (cross-site request forgery) attacks. This would allow an attacker to redirect user input to an untrusted site or hijack a user session.

### Bug Fixes


#### 400 Error With More Than 500 Nodes Pinned to a Node Group

In the console UI, when there are over 500 nodes pinned to a node group, a **Load All Nodes** button appears in the **Matching nodes** tab for that node group. Clicking the **Load All Nodes** button resulted in a 400 error in PE 3.8. This bug has been fixed and an error no longer occurs.

#### PE Console User Names Can Be Less Than Three Characters

In earlier releases, user names in the Role-Based Access Control (RBAC) service had to be at least three characters long. This bug has been fixed. User names can now be between 1 and 254 characters in length.

#### Classes With Shared Parameter Names Could Cause Schema Errors When Deleted

In PE 3.7 and PE 3.8, an error would occur if you deleted classes when the classes shared a parameter name, and a group declared both classes but only specified a value for that parameter in one of them. This series of events would make it impossible to retrieve the group.

The error also occurred if the production environment was deleted.

This bug has been fixed.

#### Using Facts to Trigger the Agent-Specified Environment

In PE 3.7 and PE 3.8, if you used a fact to trigger the agent-specified environment, it took two sequential Puppet runs before the agent-specified environment was picked up.

For example, in the **Agent-specified** environment node group, if you added a rule that matched nodes with the "test" fact set to true and then you ran Puppet, the agent would receive the environment that would have been assigned without the "test" fact set to true on the first Puppet run, and then on the second Puppet run it would receive the agent-specified environment.

This bug has now been fixed so that the agent-specified environment is triggered on the first Puppet run.

#### `X-Frame-Options DENY` Was Added to Response Headers

`X-Frame Options DENY` was added to browser headers to keep so that frames cannot be used with static console assets. This change was made to protect against potential clickjacking.

#### PE 3.7 Left Errors About the `pe-activity` Database in PostgreSQL Logs

Even if PE 3.7.x installed correctly, the PostgreSQL install logs contained error messages, which were confusing. The issue was caused by the Activity Service, and has been fixed.

#### A $ Sign in a Parameter Can Be Escaped

In earlier releases, a $ sign could not be properly escaped in a parameter value. This issue has been fixed.
