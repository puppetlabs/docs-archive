---
layout: default
title: "Getting support for Puppet Enterprise"
canonical: "/pe/latest/overview_getting_support.html"
---

## Puppet Enterprise support lifecycle

The Puppet Support Lifecycle policy provides guidelines for product support when Puppet Enterprise versions are released, and throughout the lifecycle of each version. This guide is to help customers strategically plan upgrades and version choices. 

Puppet Enterprise versions are in one of **three phases** of the Puppet Support Lifecycle. These phases are:

- **Mainstream support:** Puppet Enterprise versions in this phase receive security updates, bug fixes and the entitlement of technical support defined in the [support plan](https://puppet.com/content/puppet-enterprise-support-plans). Customers on current support plans can access all services from customer support, including the authenticated FAQ, the [customer support portal](https://support.puppet.com/hc/en-us/restricted?return_to=https%3A%2F%2Fsupport.puppet.com%2Fhc%2Fen-us) and other customer-facing sources (such as [ask.puppet.com](https://ask.puppet.com/questions/) and the [Puppet Forge](https://forge.puppet.com/)).

- **Limited support:** Puppet Enterprise versions in this phase are no longer eligible for security updates and bug fixes. Customers on valid support plans can access services from customer support, still receive SLA response times and utilize the other customer-facing resources listed above. Customers are always encouraged to upgrade to a fully-supported version of Puppet Enterprise. Puppet supports customers to upgrade to the current mainstream supported version(s) with a variety of assistance and resource options.

- **End-of-life (EOL):** Puppet Enterprise versions in this phase are no longer eligible for security updates, bug fixes, or anything other than "commercially reasonable" assistance from customer support. Customers in the process of upgrading to a fully-supported version may contact support. As with other support phases, customer-facing sources such as the community and the Puppet Forge are still available.

Puppet produces a **standard release** approximately every three months. That means customers will always have access to the latest feature and security updates. Some customers often prefer to retain the same base version for an extended period. Puppet releases a new **long term support (LTS)** release every 18 months. All **standard releases** receive **mainstream support** until the next standard release or LTS option is available. An LTS release receives mainstream support for 24 months after launch.

Puppet Enterprise makes use of open source tools and libraries in addition to commercial-only software. Projects which Puppet, Inc. owns and maintains, like Facter, the Puppet Agent and Server, and PuppetDB, are effectively "upstream" of the commercial releases, and as such will move faster and have shorter support lifecycles than Puppet Enterprise. We may discontinue updates to our open-source platform components before their commercial EOL dates. Puppet also bundles and distributes externally-maintained components, such as Ruby, Postgres, and the JVM. We vet upstream security and feature releases and will update supported versions according to customer demand and our [Product Security Policy](/security/vulnerability_submission_process.html).

Puppet provides either **mainstream** or **limited support** for the versions of Puppet Enterprise listed below. All versions older than Puppet Enterprise 3.8 are now End-of-life (EOL) and we encourage upgrading to the [latest version](https://puppet.com/download-puppet-enterprise).

PE versions     | Start mainstream support   | Start limited support      | EOL
----------------|--------------------------|--------------------------|---------
2017.1 | March 14, 2017                    | TBD                     | TBD
2016.5 | December 13, 2016                 | March 14, 2017          | TBD
2016.4 (LTS) | October 20, 2016            | October 2018            | TBD
2016.2 | June 21, 2016            | October 20, 2016            | October 31, 2017
2016.1 | April 7, 2016            | June 21, 2016               | October 31, 2017
2015.3 | December 8, 2015           | April 7, 2016             | October 31, 2017
2015.2 | July 28, 2015            | December 8, 2015            | October 31, 2017
3.8 | April 28, 2015            | December 31, 2016             | December 31, 2016

See also the [operating system support lifecycle](https://docs.puppet.com/pe/latest/sys_req_os.html#operating-system-support-life-cycles). 

## Getting support

Getting support for Puppet Enterprise is easy; it is available both from Puppet and the community of Puppet Enterprise users. We provide responsive, dependable, quality support to resolve any issues regarding the installation, operation, and use of Puppet.

There are three primary ways to get support for Puppet Enterprise:

- Check the [knowledge base for existing solutions][kb], or report issues to the [Puppet customer support portal][portal].
- Joining the Puppet Enterprise user group.
- Seeking help from the Puppet open source community.

[kb]: https://support.puppet.com/hc/en-us
[portal]: https://support.puppet.com
[lifecycle]: https://puppet.com/misc/puppet-enterprise-lifecycle/
[See the support lifecycle page for more details.][lifecycle]

### Reporting issues to the customer support portal

#### Paid support

Puppet provides two levels of [commercial support offerings for Puppet Enterprise](http://puppet.com/support-services/customer-support): Standard and Premium.  Both offerings allow you to report your support issues to our confidential [customer support portal][portal].  You will receive an account and log-on for this portal when you purchase Puppet Enterprise.

**Customer support portal: [https://support.puppet.com][portal]**

#### The PE support script

When seeking support, you may be asked to run an information-gathering support script. The script is provided by the `pe_support_script` module bundled with the PE Installer and can be run via `/opt/puppetlabs/bin/puppet enterprise support`.

This script will collect a large amount of system information, compress it, and print the location of the zipped tarball when it finishes running. We recommend that you examine the collected data before forwarding it to Puppet, as it may contain sensitive information that you will wish to redact.

The information collected by the support script includes:

- iptables info (is it loaded? what are the inbound and outbound rules?) (both ipv4 and ipv6)
- Facter output generated by `puppet facts find`
- SELinux status
- the amount of free disk and memory on the system
- hostname info (`/etc/hosts` and the output of `hostname --fqdn`)
- list of established connections from `netstat`
- output from `ifconfig`
- the umask of the system
- NTP configuration (what servers are available, the offset from them)
- OS and kernel info from `uname -a` and `lsb_release -a` (if installed)
- the state of installed PE packages as reported by the system package manager
- the current process list
- a listing of Puppet certs
- a listing of all services (except on Debian, which lacks the equivalent command)
- current environment variables
- whether the Puppet master is reachable
- the output of `mco ping` and `mco inventory`
- the output of `ulimit -a` for the `pe-activemq` user along with a count of file descriptors used by the `pe-activemq` process
- a list of all Puppet modules on the system
- the output of `puppet module changes` (shows if any modules installed by PE have been modified)
- a listing (no content) of the files in
   - `/opt/puppetlabs/`
   - `/var/opt/lib`
- reports the size of the PostgreSQL databases and relations
- the configuration used by PE RBAC to connect with a directory service, excluding sensitive information such as passwords
- r10k config files
   * version of the r10k gem installed
   * output of r10k deploy display -p --detail
- the output from [PE status API endpoints](./status_api.html)
- output from the PuppetDB `/summary-stats` endpoint, which provides non-identifying database statistics for troubleshooting

It also copies the following files:

- system logs
- the contents of `/etc/puppetlabs/` (being careful to avoid sensitive files, such as modules, manifests, ssl certs, and MCollective credentials)
- configuration files for PE services located under `/etc/sysconfig` or `/etc/default`.
- the contents of `/var/log/puppetlabs/`
- the contents of `/opt/puppetlabs/puppet/cache/state/`
- the contents of `/etc/resolv.conf`
- the contents `/etc/nsswitch.conf`
- the contents of `/etc/hosts`


### Join the Puppet Enterprise user group

<https://groups.google.com/a/puppet.com/forum/#!forum/pe-users>

- Click on “Sign in and apply for membership.”
- Click on “Enter your email address to access the document.”
- Enter your email address.


Your request to join will be sent to Puppet for authorization and you will receive an email when you’ve been added to the user group.

### Getting support from the existing Puppet community

As a Puppet Enterprise customer you are more than welcome to participate in our large and helpful open source community as well as report issues against the open source project.

- Puppet open source user group:

    <http://groups.google.com/group/puppet-users>
- Puppet Developers group:

    <http://groups.google.com/group/puppet-dev>
- Report issues with the open source Puppet project:

    <https://tickets.puppetlabs.com/browse/PUP>
