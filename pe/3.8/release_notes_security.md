---
title: "PE 3.8 » Release Notes >> Security and Bug Fixes"
layout: default
subtitle: "Security and Bug Fixes"
canonical: "/pe/latest/release_notes_security.html"
---

This page describes resolved issues in the Puppet Enterprise (PE) 3.8.x release line.

## Puppet Enterprise 3.8.7 (November, 2016)

PE 3.8.7 is a release to provide security improvements. For more information, review the [security and vulnerability announcements](/security), as well as the [known issues](./release_notes_known_issues.html).

## Puppet Enterprise 3.8.6 (August, 2016)

PE 3.8.6 is a release to provide security improvements. For more information, review the [security and vulnerability announcements](/security), as well as the [known issues](./release_notes_known_issues.html).

## Puppet Enterprise 3.8.5 (May, 2016)

For more information about this release, also see:

- [Known Issues](./release_notes_known_issues.html)
- [Security and vulnerability announcements](/security)

### Bug Fixes

### Live Management operations resulted in a 403 error

Certain Live Management operations that use PUT, POST, and DELETE returned a 403 error. This issue is resolved in this release. <!--PE-14671-->

#### Certificate signing through the console resulted in a 403 error

When attempting to sign node certificates in the console, an error appeared with the message "Could not process this request." This issue is resolved in this release. <!--PE-14230-->

#### Razor didn't consistently match MAC addresses

In previous releases, the `macaddress` fact wasn't correctly matched to nodes when multiple interfaces on the node reported different MAC addresses. The new `has_macaddress` operator searches all facts with the `macaddress` prefix, enabling better matching of [tags](.razor_tags.html). We recommend using the `has_macaddress` operator instead of the `macaddress` fact alone. (RAZOR-732)

#### External Directory Groups Could Not Be Imported When Using SSL Certificate Verification

If you [configured PE to verify directory server certificates](./rbac_ldap.html#verify-directory-server-certificates), PE would fail to import user groups. This was due to an incorrect value that RBAC attempted to pass to the directory server. This bug has been fixed. RBAC now passes the correct value to the directory server and you can successfully import user groups when PE is configured to verify the directory server certificate.

## Puppet Enterprise 3.8.4 (February 9, 2016)

### Security Fixes

#### Fixed issues for January 2016 Rails security announcement

- Posted February 9, 2016
- Assessed Risk Level: Medium

On January 25, 2016, the Ruby on Rails project announced several security vulnerabilities in Rails.

Puppet Enterprise versions prior to 3.8.4 contained vulnerable versions of Rails. Puppet Enterprise 3.8.4 contains an updated version of Rails that has patched the vulnerabilities.

For more information, see the [Ruby on Rails security announcement](http://weblog.rubyonrails.org/2016/1/25/Rails-5-0-0-beta1-1-4-2-5-1-4-1-14-1-3-2-22-1-and-rails-html-sanitizer-1-0-3-have-been-released/).

This is fixed in Puppet Enterprise 3.8.4.

#### CVE-2016-0701: Fixed issues for January 2016 OpenSSL security announcement

- Posted February 9, 2016
- Assessed Risk Level: Medium

On January 28, 2016 OpenSSL announced several security vulnerabilities. Puppet agent, Puppet (Windows), and Puppet Enterprise include a vulnerable version of OpenSSL. OpenSSL has been updated to address CVE-2016-0701.

For more information, see the [OpenSSL announcement](https://www.openssl.org/news/secadv/20160128.txt).

This issue is fixed in Puppet Enterprise 3.8.4.

#### CVE-2015-7519: Passenger vulnerability

- Posted February 2, 2016
- Assessed Risk Level: Medium

Puppet Enterprise versions earlier than 3.8.4 use a vulnerable version of Passenger in the PE console.

To address this vulnerability, we've updated Puppet Enterprise 3.8.4 to the latest version of Passenger.

For more information, see the [passenger security announcement](https://blog.phusion.nl/2015/12/07/cve-2015-7519/).

This is fixed in Puppet Enterprise 3.8.4.

#### CVE-2015-5254: Vulnerability in ActiveMQ web console

The default configuration of Puppet Enterprise is not affected by the December 2015 ActiveMQ security announcement. However, if you have enabled the ActiveMQ web console you may be vulnerable.

Puppet Enterprise 3.8.4 includes an updated version of ActiveMQ to address this vulnerability.

For more information, see the [ActiveMQ security announcement](http://activemq.apache.org/security-advisories.data/CVE-2015-5254-announcement.txt).

This issue is fixed in Puppet Enterprise 3.8.4.

#### CVE-2015-7551: Unsafe tainted string usage in Fiddle and DL

- Posted February 2, 2016
- Assessed Risk Level: Low

Puppet Enterprise 3.8.4, includes an update to address this vulnerability.

For more information, see the [Ruby security announcement](https://www.ruby-lang.org/en/news/2015/12/16/unsafe-tainted-string-usage-in-fiddle-and-dl-cve-2015-7551).

This issue is fixed in Puppet Enterprise 3.8.4.

#### Fixed issues for January 2016 Java security announcement

- Posted February 2, 2016
- Assessed Risk Level: Medium

Puppet Enterprise 3.8.4 includes updates to address the Java security announcement.

For more information, see [the Oracle Java announcement](http://www.oracle.com/technetwork/topics/security/cpujan2016-2367955.html#AppendixJAVA).

This issue is fixed in Puppet Enterprise 3.8.4.

### Bug Fixes

#### Fixed: Puppet Agent Service Did Not Properly Stop When Upgrading Compile Masters on Ubuntu Platforms

Previously, if you attempted to upgrade Ubuntu compile masters, you may have encountered a situation in which the Puppet agent service was not properly stopped when the new agent was installed (which is part of the upgrade process). When it was time for the agent service to start and run Puppet, an error was raised indicating a run was already in progress: `Notice: Run of Puppet configuration client already in progress; skipping (/var/opt/lib/pe-puppet/state/agent_catalog_run.lock exists)`.

This was due to the fact that the agent service didn't stop. This caused the upgrade to fail.

#### Fixed: `max-requests-per-instance` Caused a Memory Leak

When using the optional `max-requests-per-instance` setting in `puppetserver.conf`, Puppet Server should flush the JRuby instance from memory and replace it with a fresh instance after serving a number of requests defined by that setting. This can be handy when dealing with memory leaks in module code.

However, the outgoing JRuby instances were not flushed as expected during garbage collection, resulting in Puppet Server causing a memory leak that could destabilize the server after a sufficient number of replacement cycles.

#### Windows Platform Switched to rubyw to Run MCollective Commands in Powershell

For the Windows platform, we've switched to rubyw, a Windows-specific ruby. This allows you to run MCollective commands that will return exit codes Powershell can read.

#### Fixed: Razor `delete-repo` command didn't remove the repository folder from the server

In previous releases, the `delete-repo` command removed the repository from the Razor database, but didn't delete downloaded files on disk. With this release, if you supplied the `iso_url` property when you created the repository, the repository folder is also deleted from the server.

### Puppet agent release notes

This version of PE includes Puppet agent version 1.3.4, released January 25, 2016. Refer to the [Puppet agent release notes](/puppet/4.3/reference/release_notes_agent.html#puppet-agent-134) for more information.

## Puppet Enterprise 3.8.3 (November 5, 2015)

### Security Fixes

#### Oracle Java October 2015 Security Fixes

- Posted November 5, 2015
- Assessed Risk Level: Medium

On October 20th, Oracle announced several security vulnerabilities in Java. Puppet Enterprise versions prior to 3.8.3 contained affected versions of Java. Puppet Enterprise 3.8.3 contains updated Java packages that address the vulnerabilities. For more information about the Java vulnerabilities, refer to the [Oracle security announcement](http://www.oracle.com/technetwork/topics/security/cpuoct2015-2367953.html#AppendixJAVA).

This issue affects PE 3.x.
This issue is fixed in PE 3.8.3.

#### PostgreSQL October 2015 Security Fixes

- Posted November 5, 2015
- Assessed Risk Level: Low

In October, the PostgreSQL project announced several security vulnerabilities in the PostgreSQL database server. Puppet Enterprise versions prior to 3.8.3 contained affected versions of PostgreSQL. Puppet Enterprise 3.8.3 contains updated PostgreSQL packages that address the vulnerabilities. For more information about the PostgreSQL vulnerabilities, refer to the [PostgreSQL security announcement](http://www.postgresql.org/about/news/1615/).

This issue affects PE 3.x.
This issue is fixed in PE 3.8.3.

#### CVE-2015-7328 World-readable CA Key in Puppet Server

- Posted November 5, 2015
- Assessed Risk Level: Low

During the initial installation and configuration of Puppet Enterprise, there is a short window of time where the generated CA key is left world-readable. This is corrected later during the configuration/bootstrapping steps.

In Puppet Enterprise 3.8.3, the CA key (and all other SSL private keys) are created with the correct permissions.

This issue affects PE 3.8.x.
This issue is fixed in PE 3.8.3.

### Bug Fixes

#### Fixed: ActiveMQ Networks of Brokers Collapse at Scale

This release corrects and issue for environments with more than 1500 nodes in which ActiveMQ hub and spoke topologies did not properly scale. The fix was to switch from duplex to simples ActiveMQ network connectors.

#### Fixed: Agent Installation Getting Agent Package for Wrong Location

Previously if you placed the pe-agent package in your own repository, it may have been used instead of the repository on the Puppet master when when you ran the installation script. This fix ensures the script retrieves agent packages from the Puppet master.

#### Fixed: Puppet 3.5.0 Introduced Tag Filtering Error for Catalog Runs

This release of PE includes a hotfix to correct an issue in which Puppet 3.5.0 introduced an error to agent tag filtering. In this case, a single tag, `--tag foo::bar`, would be split on the namespace separator `::` and expanded to a set of `['foo::bar', 'foo', 'bar']`.

## Puppet Enterprise 3.8.2 (August 6, 2015)

### Security Fixes

#### CVE-2014-6272 - Potential Heap Overflow Vulnerability in Libevent

- Posted August 6, 2015
- Assessed Risk Level: Medium

Earlier this year, the Libevent project announced a security vulnerability in the Libevent evbuffer APIs. Puppet Enterprise versions prior to 3.8.2 contained a vulnerable version of Libevent. Puppet Enterprise 3.8.2 contains an updated version of Libevent that has fixed the vulnerability.

For more information about the vulnerability, please refer to [CVE-2014-6272](http://archives.seul.org/libevent/users/Jan-2015/msg00010.html).

This issue affects PE 3.x.
This issue is fixed in PE 3.8.2.

#### CVE-2015-3183 - HTTP Request Smuggling Vulnerability in Apache HTTP Server

- Posted August 6, 2015
- Assessed Risk Level: Low

A flaw in Apache HTTP Server's parsing of chunked requests could allow HTTP request smuggling attacks. Puppet Enterprise versions prior to 3.8.2 contained vulnerable versions of Apache HTTP Server. Puppet Enterprise 3.8.2 contains an updated version of Apache HTTP Server that has fixed the vulnerability.

For more information about the vulnerability, please refer to [CVE-2015-3183](http://httpd.apache.org/security/vulnerabilities_22.html).

This issue affects PE 3.x.
This issue is fixed in PE 3.8.2.

#### Ruby on Rails Project June 2015 Security Fixes

- Posted August 6, 2015
- Assessed Risk Level: Medium

On June 16th, the Ruby on Rails project announced several security vulnerabilities in Rails and Rack. Puppet Enterprise versions prior to 3.8.2 contained vulnerable versions of Rails and Rack. Puppet Enterprise 3.8.2 contains updated Rack and Rails that have patched the vulnerabilities.

For more information about the vulnerabilities, please refer to the [Ruby on Rails security announcement](http://weblog.rubyonrails.org/2015/6/16/Rails-3-2-22-4-1-11-and-4-2-2-have-been-released-and-more/).

This issue affects PE 3.x.
This issue is fixed in PE 3.8.2.

#### Advisory: `pe-java` Was Not Updated on the Console Node on Split Upgrades

In "split" configurations (where the Master, console, and PuppetDB are installed on separate nodes), upgrades of Puppet Enterprise did not upgrade `pe-java` on the console node.

Users with this configuration should upgrade to Puppet Enterprise 3.8.2. Users who are unable to upgrade to Puppet Enterprise 3.8.2 should manually upgrade 'pe-java' on the console node.

This issue affects PE 3.7.x and 3.8.x.
This issue is fixed in PE 3.8.2.

#### Oracle Java July 2015 Security Fixes

- Posted July 28, 2015
- Updated August 6, 2015
- Assessed Risk Level: Medium

On July 14th, Oracle announced several security vulnerabilities in Java. PE versions prior to PE 3.8.2 contained a vulnerable version of Java. PE 3.8.2 and PE 2015.2.0 contain an updated version of Java that has patched the vulnerabilities. For more information about the Java vulnerabilities, refer to the [Oracle security announcement](http://www.oracle.com/technetwork/topics/security/cpujul2015-2367936.html#AppendixJAVA).

This issue affects PE 3.x.
This issue is fixed in PE 3.8.2 and PE 2015.2.0.

### Bug Fixes

#### Querying the Nodes Endpoint of the Classifier Service Could Exhaust Memory

If a large number of nodes were returned when querying the `v1/nodes` endpoint of the classifier service API, the pe-console-services process could exhaust the memory and return a 500 error.

This issue was resolved in PE3.8.3. Now, when querying the `v1/nodes` endpoint, you get the same list of nodes as before, but you only get the most recent check-in information for each node rather than all previous check-ins.

#### A $ Sign in a Parameter Can Be Escaped

In earlier releases of PE 3.8, a $ sign could not be properly escaped in a parameter value. This issue has been fixed.

#### Solaris Zone Resource Failed When Using `iptype => exclusive`

A fix was created for pe-puppet to address the issue where a Solaris zone resource failed if the Puppet manifest contained  `iptype => exclusive`. The fix applies to Solaris 11.

#### PowerShell Execs Failed When Puppet Agent Was Run from MCollective

Puppet agent was causing no-op agent runs on Windows to fail to execute PowerShell provider execs. The MCollective Puppet plugin was updated to execute Windows processes on a new console.

#### Customers Were Unable to Use Negated Command Aliases in `/etc/sudoers`

When negated command aliases were present in `/etc/sudoers`, customers were unable to use Puppet with Augeas to manage the file. This fix allows negated command aliases when using Augeas to control `sudoers`.

#### KahaDB Didn't Clean Up Old Log Files and They Filled Up Disk Space

The `max-frame-size` setting is now applied to PuppetDB's ActiveMQ consumer thread. It was previously set to the default, which could cause errors when extremely large commands were submitted.

#### PE 3.3.2 and Older Agents Couldn't Compile Catalogs Against Masters with "Future" Parser Enabled

The problem was due to facts as strings in older versions being compared to facts that are numerics in the updated parser. The fix converts fact strings to an integer.

#### `pe-puppet-dashboard-workers` Emitted an Extra Operand Error on CentOS 6.6 Startup

This fix updated the `pe-puppet-dashboard-workers` init script to pass `--check` to the daemon function to check that `$name` was running instead of the first argument
passed to daemon.

#### Duplicate Issue with `symlink` Class Even When `manage_symlink` is `false`

When managing `/usr/local/bin` in PE 3.8, there was a conflict with the new `symlink` class even if `manage_symlink` variable was defined as `false`. This has been fixed.

#### Puppet Will Purge `/etc/hosts` If It Has Invalid Lines

If there was some invalid text in `/etc/hosts`, such as double quotes around the IP address, hostname, or `host_alias`, Puppet would purge the file and create a new one. This is now fixed.

## Puppet Enterprise 3.8.1 (June 18, 2015)

### Security Fixes

#### OpenSSL June 2015 Security Fixes

- Posted June 18, 2015
- Assessed Risk Level: Medium

On June 11, the OpenSSL project announced several security vulnerabilities in OpenSSL. Puppet Enterprise versions prior to 3.8.1 contained vulnerable versions of OpenSSL. Puppet Enterprise 3.8.1 contains updated versions of OpenSSL that have patched the vulnerabilities.

For more information about the OpenSSL vulnerabilities, refer to the [OpenSSL security announcement](https://www.openssl.org/news/secadv_20150611.txt).

This issue affected Puppet Enterprise 2.x (Windows and Solaris agents only) and 3.x (All platforms).

The issue is resolved in Puppet Enterprise 3.8.1

#### PostgreSQL May 2015 Security Fixes

- Posted June 18, 2015
- Assessed Risk Level: Medium

On May 22, the PostgreSQL project announced several security vulnerabilities in PostgreSQL. The potential impact of these vulnerabilities includes denial of service and information leakage.

Puppet Enterprise versions prior to 3.8.1 contained a vulnerable version of PostgreSQL. Puppet Enterprise 3.8.1 contains an updated version of PostgreSQL.

For more information about the PostgreSQL vulnerabilities, refer to the [PostgreSQL security announcement](http://www.postgresql.org/about/news/1587/).

This issue affected Puppet Enterprise 3.x.

This issue is resolved in Puppet Enterprise 3.8.1

#### Apache ActiveMQ February 2015 Security Fixes

- Posted June 18, 2015
- Assessed Risk Level: Medium

In February, the Apache ActiveMQ project announced several security vulnerabilities in ActiveMQ.

Puppet Enterprise versions prior to 3.8.1 contained a vulnerable version of ActiveMQ. Puppet Enterprise 3.8.1 contains an updated version of ActiveMQ that has patched the vulnerabilities.

For more information about the ActiveMQ vulnerabilities fixed in this release, refer to the [Active MQ security announcements for CVE-2014-3600](http://activemq.apache.org/security-advisories.data/CVE-2014-3600-announcement.txt), [CVE-2014-3612](http://activemq.apache.org/security-advisories.data/CVE-2014-3612-announcement.txt), and [CVE-2014-8110](http://activemq.apache.org/security-advisories.data/CVE-2014-8110-announcement.txt).

This issue affected Puppet Enterprise 3.x.

This issue is resolved in Puppet Enterprise 3.8.1.

#### CVE-2015-4100 - Puppet Enterprise Certificate Authority Reverse Proxy Vulnerability

- Posted: June 18, 2015
- Assessed Risk Level: Medium

In a specific multimaster configuration, Puppet Enterprise 3.7.x and 3.8.0 are potentially vulnerable to an issue where any client certificate trusted by the master could be used to perform full certificate management.

In the vulnerable configuration, the compile master(s) would need to have been added to the `certificate-authority.client-whitelist` setting for the CA server.

The vulnerable configuration allows any agent authenticated by the master to revoke the certificates of other nodes, causing a denial of service. An attacker could also approve pending certificate requests for other nodes, potentially exposing Puppet catalogs containing sensitive data.

Default "monolithic", "split", and multimaster installs of PE 3.7.x or PE 3.8.0 are not affected.

The vulnerability is resolved by default in Puppet Enterprise 3.8.1.

This issue affects Puppet Enterprise 3.7.x and 3.8.0.

This issue is resolved in Puppet Enterprise 3.8.1.


#### CVE-2015-3900, CVE-2015-4020 - Request Hijacking Vulnerability in RubyGems

- Posted: June 18, 2015
- Assessed Risk Level: High

A vulnerability in RubyGems versions between 2.0 and 2.4.6 left clients open to a DNS hijack attack. An attacker could exploit this vulnerability to force a client to unknowingly download and install malicious gem content from an attacker-controller gem server.

This issue affects Puppet Enterprise 3.7.x, 3.8.0, including Puppet Server, Razor, and Windows 64-bit Puppet Agent.

This issue is resolved in Puppet Enterprise 3.8.1.


#### CVE-2015-4000 - Logjam TLS Vulnerability

- Posted: June 18, 2015
- Assessed Risk Level: Medium

On May 20, researchers announced weaknesses in how Diffie-Hellman for TLS is commonly deployed.

Fixes for this vulnerability disable export-grade cipher suites, utilize Elliptic-Curve Diffie-Hellman (ECDH) key exchange, and use 2048-bit or stronger Diffie-Hellman groups using "safe" primes.

For more information on Logjam, please see the [Logjam disclosure site](https://weakdh.org).

See instructions on [how to generate your own DHParams file](./trouble_dh_generate.html) if you prefer.


This issue affected Puppet Enterprise 3.x and 3.8.0.


This issue is resolved in Puppet Enterprise 3.8.1.


### Bug Fixes

#### Environments Are Listed Even If They Cannot Be Loaded

In PE 3.8.0, if an environment could not be loaded due to an error, such as a syntax error in the code in that environment, the environment would not appear in the environments dropdown list in the PE console UI.

This bug has been fixed.

#### Calling Inspect on a Scope Generated a Massive String

An inspect function was added to the `Puppet::Parser::Scope` class that
aliases to the `to_s` method so that calling inspect on a scope no
longer generates a massive, unused string.

#### MCollective Was Core Dumping on AIX

Several customers reported that `pe-ruby` intermittently core dumped
with MCollective on AIX, with a variety of Ruby backtraces. We rebased the AIX PE agent stack to Ruby 2.1.6 and this appears to eliminate or greatly reduce the frequency of the issue.

#### AIX Agent Installer Failed on Systems With Newer Curl Installed

This fix updates the AIX install script that checks the version of curl being used and makes the version-appropriate invocation. This is necessary because curl is not a native binary on AIX and could be installed many different ways.

#### Puppet Server Failed to Start on Some Ubuntu versions

Puppet Server failed to start on some Ubuntu variants when libcrypt.so was not found. To fix this, `pe-java` was updated with the default `LIBPATH` patch used for system Java on Debian machines.

#### Symlinks Were Disabled, But Were Then Recreated After a Puppet Agent Run

Symlinks were still created, despite `manage_symlinks` being set to `false`. Adding the `puppet_enterprise::manage_symlinks` parameter to the `puppet_enterprise::profile::mcollective::console` class fixed the problem where `puppet_enterprise::profile::mcollective::console` ignored `manage_symlinks=false`. Now,  symlinks are not created when `manage_symlinks` is set to `false`.

#### Creating Symlinks Modified Ownership and Permission of `/usr/local`

Resource defaults are dynamically scoped, meaning the file resources in
`puppet_enterprise::symlinks` could inherit bad defaults from including
classes. In order to explicitly not manage owner/group/mode,
we injected an explicit lack of defaults.

#### PuppetDB 2.3.2 Was Not Updating Facts

An issue prevented fact storage for nodes under certain circumstances. This issue was fixed in a bugfix release, PuppetDB 2.3.4.1.

#### Classification Migration Tool Didn't Identify Source of Failure

Previously, the PE classification data migration tool failed when encountering invalid yaml in values stored in the PE console database (as was allowed). The failure came with no details to describe what caused the failure. The PE classification data migration tool now exits gracefully when it encounters this situation, and notifies the user of the specific value requiring modification. The user is asked to fix the yaml string.

#### SLES 11 Installation Failed on 64 Bit Machine

The installer was comparing shmmax required settings as a string to an integer. As a result of this fix, the installer now compares them both as an integer.

#### `concatfragments.sh` Retained the Old GID Value

Previously, the group wasn't managed and this caused it to be set to the GID of the `pe-puppet` user on the master, which isn't necessarily a valid group on an agent machine.

With this fix, the group is set to the GID of the user running Puppet. Typically, this will be root.

#### Zypper Was Not the Default Package Provider for SUSE OS Family

This fix makes Zypper the default package provider for SUSE systems, such as SLES 11 and newer. This prevents Puppet agent from selecting a provider using an alphabetical sort, which Zypper always lost.

## Puppet Enterprise 3.8 (April 28, 2015)

### Security Fixes

#### CWE - Cross-Frame Scripting (XFS) Vulnerability

Posted April 28, 2015

Some endpoints in the Puppet Enterprise console are potentially susceptible to cross-frame scripting (XFS) attacks. An exploit would coerce a PE user into navigating to an attacker-controlled web page that loaded the Puppet Enterprise console in an HTML frame.

This issue affected PE 3.7.x. It's resolved in PE 3.8.0.

#### OpenSSL March 2015 Security Fixes

Posted April 28, 2015

On March 19, the OpenSSL project announced several security vulnerabilities in OpenSSL. Puppet Enterprise versions prior to 3.8.0 contained vulnerable versions of OpenSSL. Puppet Enterprise 3.8.0 contains updated versions of OpenSSL that have patched the vulnerabilities.

For more information about the OpenSSL vulnerabilities, refer to the [OpenSSL security announcement](https://www.openssl.org/news/secadv_20150319.txt).

These issues affected PE 2.x and 3.x. They're resolved in PE 3.8.0.

#### Oracle Java April 2015 Security Fixes

Posted April 28, 2015

On April 14th, Oracle announced several security vulnerabilities in Java. Puppet Enterprise versions prior to 3.8.0 contained a vulnerable version of Java. Puppet Enterprise 3.8.0 contains an updated version of Java that has patched the vulnerabilities.

For more information about the Java vulnerabilities, refer to the [Oracle security announcement](http://www.oracle.com/technetwork/topics/security/cpuapr2015-2365600.html).

These issues affected PE 3.x. They're resolved in PE 3.8.0.

#### PostgreSQL February 2015 Security Fixes

Posted April 28, 2015

On February 5th, the PostgreSQL project announced several security vulnerabilities in PostgreSQL. The impact of these vulnerabilities includes information leakage, denial of service, SQL injection, and possible privilege escalation. However, all of the vulnerabilities require prior database authentication.

Puppet Enterprise versions prior to 3.8.0 contained a vulnerable version of PostgreSQL. Puppet Enterprise 3.8.0 contains an updated version of PostgreSQL that has patched the vulnerabilities.

For more information on the PostgreSQL vulnerabilities, please see the [PostgreSQL announcement](http://www.postgresql.org/about/news/1569/).

These issues affected PE 3.x. They're resolved in PE 3.8.0.

#### CVE - Ruby OpenSSL Hostname Verification

Posted April 28, 2015

Vulnerabilities in Ruby’s OpenSSL extension allow overly permissive matching of hostnames, particularly when using wildcard SSL certificates.

Puppet Enterprise does not generate wildcard SSL certificates by default. However, if a PE infrastructure has been configured with wildcard SSL certificates, it could theoretically be vulnerable to man-in-the-middle attacks.

For more information on the vulnerability, please see the [Ruby project’s announcement](https://www.ruby-lang.org/en/news/2015/04/13/ruby-openssl-hostname-matching-vulnerability/).

These issues affected PE 3.x and Puppet-Agent 1.0. They're resolved in PE 3.8.0.

#### CVE - LibYAML Vulnerability Could Allow Denial of Service

Posted April 28, 2015

A flaw in the LibYAML library’s parsing of wrapped strings could allow an attacker to cause a denial of service by loading a specially crafted YAML document.

This issue affected PE 3.x. It's resolved in PE 3.8.0

### Bug Fixes

Puppet Enterprise 3.8 contains a number of performance and documentation improvements, in addition to the fixes that are highlighted below.

#### Static Defaults Were Set for TK-Jetty `max threads`

Previously, in PE, tk-jetty `max threads` were set to a static default of 100 threads. We determined this was too low for large environments with high CPU counts, and we no longer set a default for `max threads`. Unless you specify a value, we will not manage or define this setting. You can tune `max threads` for the [PE console/console API](./console_config.html#tuning-max-threads-on-the-pe-console-and-console-api) or [Puppet Server](./config_puppetserver.html#tuning-max-threads-on-puppet-server) as needed using Hiera.

#### Custom Console Cert Functionality Was Broken in PE 3.7.x

In PE 3.7.x users could not set custom certificates for the PE console. This release corrects that issue, but if you're upgrading from PE 3.3.2, note that cert functionality has changed in PE 3.8. If needed, refer to [Configuring the Puppet Enterprise Console to Use a Custom SSL Certificate](./custom_console_cert.html) for instructions on reconfiguring your custom console certificate.

#### Agent Installation No Longer Sets `environment = production`

This release corrects an issue in which agents' environments were set to `production` during the installation process (in `puppet.conf`). This behavior was undesirable if users wanted to set agents' environments with the node classifier.

#### Agent Server Setting Moved to `[main]` in `puppet.conf`

The agent server setting is now set in the `[main]` section of `puppet.conf`; in previous versions, it was set in the `[agent]` section. This fix makes it possible to use the agent installation script to install compile masters and ActiveMQ hubs and spokes in large environment installations.

#### ActiveMQ Spokes Can Be Managed from Profile Level

ActiveMQ spokes (brokers) can now have their collectives set and managed in PE from the profile level, using the `excluded_collectives` and `included_collectives` parameters in the `puppet_enterprise::profile::amq::broker` class. This functionality was not available from the profile level in PE 3.7.x.

#### ActiveMQ/MCollective Network Connections Failed when Sending Commands to Targeted Spokes

Due to a misconfigured setting in PE (`puppet_enterprise::amq::config::network_connector`), ActiveMQ/MCollective network connections failed on occasion. Specifically, the connections failed when a ActiveMQ hub was sending commands to targeted spokes (brokers) in large environment installations. This is fixed in PE 3.8.

#### Changes to ActiveMQ Heap Size Did Not Restart `pe-activemq` Service

In PE 3.7.x, when users changed the ActiveMQ heap size, the change did not trigger the `pe-activemq` service to restart, and thus PE did not pick up the changes. PE 3.8 corrects this issue, and the service is now restarted when changes are made.

#### Fix for Tuning Classifier Synchronization Period

This fix raises the node classifier's default synchronization period from 180 to 600 seconds. It also introduces a [tunable setting](./console_config.html#tuning-the-classifier-synchronization-period) via the PE console.

#### Setting `https_proxy` Prevented Service Checks During Installation/Upgrade with `curl`

When users ran the PE installer/upgrader behind a proxy, PE could not properly `curl` PE services during installation/upgrade. This fix corrects the issue by unsetting `HTTPS_PROXY`, `https_proxy`, `HTTP_PROXY`, and `http_proxy` before performing `curl` commands to PE services during installs/upgrades.

#### Node Classifier Ignored Facts That Were False

When creating node matching rules in PE 3.7, the node classifier ignored all facts with a boolean value of `false`. For example, if you created a rule like `is_virtual` `is` `false`, the rule would never match a node. This issue has been resolved in PE 3.8.

#### Browser Crashing Issue When Returning a Null Value for `inherited_role_ids`

In PE 3.7.2, the browser would crash when the `users` endpoint for Role-Based Access Control (RBAC) returned a `NULL` value for `inherited_role_ids`. A `NULL` value is returned when you delete the user roles for a user group and then view the user. In PE 3.8, this has been fixed and the browser no longer crashes.

#### Browser Crashing Issue When Deleting a Node Group That Had a Permission Assigned

In PE 3.7, pages didn't completely render if you took the following steps:

-- Create a new user role in RBAC.
-- Add a permission to the user role that has the **Node groups** permission type.
-- Select a specific node group instance that the permission applies to.
-- Delete the node group instance that the permission applies to.
-- Try to go to the **Permissions** tab for the new user role.

The issue was that the Javascript rendering the page stops prematurely.

In PE 3.8, this has been fixed so that the page renders properly. In the PE 3.8 console, if you go to the **Permissions** tab for the new role that was added, the deleted node group instance is displayed as a dash in the **Object** column.

#### Node Classifier Returned Activity Service Error When Importing a Large Number of Groups

In PE 3.7, an error indicating that the "index row size exceeds maximum" was returned when importing a large number of groups (for example, when upgrading a large environment to PE 3.7). This error was returned even if the import was successful. In PE 3.8, this error no longer occurs.

#### Newly Created Node Group Did Not Appear in List of Parents

When you created a new node group in the PE 3.7 console, then immediately created another new node group and tried to select the first node group as the parent, the first node group did not appear in the list of selectable names in the **Parent name** drop-down list. The first node group would appear if you reloaded the page. This issue has been fixed in PE 3.8.

#### Could Only Accept Or Reject One Node at a Time

In PE 3.7, if you had multiple node requests pending, you could accept/reject one node, but if you then tried to accept/reject subsequent nodes, a 403 Forbidden error was returned. This meant that if you wanted to accept/reject node requests one at a time, you had to refresh the page after each time you accepted/rejected a node request. In PE 3.8, this issue has been fixed, and you will no longer receive a 403 Forbidden error.

#### User and Group Lookup Attributes Were Case Sensitive

In earlier releases, the RBAC service required you to use the correct case when specifying lookup attributes for external directories. In PE 3.8, lookup attributes are not case sensitive.

#### MCollective `puppet-agent` Plugin Now Works When Puppet Service Is Running

Previously, the MCO puppet agent plugin didn't let a user trigger a remote `noop` run or specify an environment if the `pe-puppet` service was running. It assumed that we were using cron instead of the `pe-puppet` service. Now you can trigger a run while the service is running.

#### Issue Creating Multiple Mirrors in zpool Resource

The fix enables you to differentiate between single and multi-mirror and raidz pools and ensures that `mirror` and `raidz` can be used as subcommands.

#### `/var/lib/peadmin/.mcollective.d` Has the Wrong Permissions

In PE 3.7.0 the `.mcollective.d` directory was assigned a `0400` permission. This created an issue when the client.log was full, and `peadmin` needed to create another one for shifting the logfile. If a logfile was full, and you tried to use MCollective to issue commands, you'd get an error message that said log shifting had failed and permission was denied. The fix sets the permissions to u+rwx for the PE admin user.

#### `pe-mcollective-metadata` Cron Redirected Output to a Path That Was Not Valid on AIX

The fix updated the manifest to use `puppet_enterprise::params::mco_logdir`, which evaluates to the appropriate log directory for AIX. The cron job now points at the correct location, `/opt/freeware/var/log/pe-mcollective`.

#### PE Removed Unmanaged Crontab Entries from Root If Invalid Entries Were Present

Due to this bug, the MCollective metadata cron job was unconditionally laid
down during PE installation. This caused problems if invalid root crontabs were present. It would then trigger a bug that removed any unmanged cron entries. The fix adds a toggle that allowed the cron job to be unmanaged, to avoid triggering this bug. An exec was
also added to ensure that the fact cache is present so that `pe-mcollective` can start.



