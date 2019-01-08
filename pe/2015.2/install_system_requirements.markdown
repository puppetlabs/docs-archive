---
layout: default
title: "PE 2015.2 » Installing » System Requirements"
subtitle: "System Requirements and Pre-Installation"
canonical: "/pe/latest/install_system_requirements.html"
---

Before installing Puppet Enterprise:

* Ensure that your nodes are running a supported operating system.
* Ensure that your Puppet master and console servers are sufficiently powerful (see the [hardware section](#hardware-requirements) below).
* Ensure that your network, firewalls, and name resolution are configured correctly and all target servers are communicating.
* Plan to install the Puppet master server before the console server, and the console server before any agent nodes. If you are separating components, install them in this order:
    1. Puppet Master
    2. PuppetDB and PostgreSQL
    3. Console
    4. Agents

## Supported Operating Systems

Puppet Enterprise runs on the following platforms:

### Puppet Master Platforms

Operating system             | Version(s)                              | Arch
-----------------------------|-----------------------------------------|-----------
Red Hat Enterprise Linux     | 6, 7                                    | x86\_64
CentOS                       | 6, 7                                    | x86\_64
Oracle Linux                 | 6, 7                                    | x86\_64
Scientific Linux             | 6, 7                                    | x86\_64
SUSE Linux Enterprise Server | 11, 12                                  | x86\_64
Ubuntu                       | 12.04, 14.04                            | x86\_64

### Puppet Agent Platforms

Operating system             | Version(s)                              | Arch
-----------------------------|-----------------------------------------|-------------------------------
Red Hat Enterprise Linux     | 4, 5, 6, 7                              | x86\_64 (i386 for 5, 6)
CentOS                       | 5, 6, 7                              | x86\_64 (i386 for 5, 6)
Oracle Linux                 | 5, 6, 7                                 | x86\_64 (i386 for 5, 6)
Scientific Linux             | 5, 6, 7                                 | x86\_64 (i386 for 5, 6)
SUSE Linux Enterprise Server | 10, 11, 12                              | x86\_64 (i386 for 10, 11)
Solaris                      | 10, 11                                      | SPARC & i386
Ubuntu                       | 10.04, 12.04, 14.04, 15.04                     | x86\_64 and i386
Debian                       | Squeeze (6), Wheezy (7), Jessie (8)                 | x86\_64 and i386
Microsoft Windows (Server)   | 2008, 2008R2, 2012, 2012R2, 2012R2 core | x86\_64  and i386
Microsoft Windows (Consumer OS) | Vista, 7, 8.1                               | x86\_64  and i386
Mac OS X                     | 10.9, 10.10                             | x86\_64
AIX                          | 5.3, 6.1, 7.1	                       | Power	

>**Note**: Some operating systems require an active subscription with the vendor's package management system (e.g., the Red Hat Network) to install dependencies.

### Supported Network Devices

Puppet Enterprise supports running Puppet agents on the following networking devices/operating systems:

- **Arista EOS**: Refer the [aristanetworks/netdev_stdlib_eos module](https://forge.puppetlabs.com/aristanetworks/netdev_stdlib_eos#limitations) on the Puppet Forge for system requirements and dependencies.
- **Cumulus Linux**: Refer to the [Cumulus Linux page](https://forge.puppetlabs.com/cumuluslinux/) on the Puppet Forge for system requirements and dependencies.


## Puppet Agent and Operating System Support Life Cycles

In PE 2015.2 and Puppet 4.0 and onwards, the same Puppet agent packages are used in our open source and Puppet Enterprise ecosystems. Because of this, we've set the following guidelines for managing Puppet agent life cycles.

#### Community-Supported Operating Systems

Puppet agents that run on community-supported operating systems will follow the life cycles determined by the suppliers of the operating systems. Essentially Puppet Labs will stop supporting an agent within 30 days of that platform's end-of-life (EOL) date. For example, Fedora 20 goes EOL June 23, 2015. This means that on or around June 23, Puppet Labs will no longer provide fixes, updates, or support for either the Puppet Enterprise or Open Source Puppet versions of that agent.

This includes the following community-supported operating systems:

- Debian
- Fedora (only available in Open Source Puppet)
- Ubuntu (non LTS) (only available in Open Source Puppet)

#### Enterprise-class Operating Systems

Puppet agents that run on enterprise operating systems will follow the extended support life cycles determined by the suppliers of the operating systems. After an enterprise-class agent platform reaches EOL, Puppet Labs *may* continue to support it in Puppet Enterprise. Please note that supporting an OS past EOL is solely at the discretion of Puppet Labs.

This includes the following enterprise-class operating systems:

- CentOS
- Mac OS X
- Oracle Enterprise Linux
- Red Hat Enterprise Linux
- Scientific Linux
- Suse Linux Enterprise Server
- Solaris
- Ubuntu LTS
- Microsoft Windows Server

## Performing Major OS Upgrades When Puppet Enterprise is Installed

In this context, a major OS upgrade refers to an upgrade to a new whole version, such as an upgrade from CentOS 6.0 to CentOS 7.0; it does not refer to a minor version upgrade (e.g., CentOS 6.5 to CentOS 6.6). A major OS upgrade typically requires [a new version of PE](https://puppetlabs.com/misc/pe-files).

Performing major upgrades of your OS while PE is installed can cause problems with PE. To perform an OS upgrade, you’ll need to perform the following steps:

1. [Back up](./maintain_backup_restore.html#back-up-your-database-and-puppet-enterprise-files) your databases and other PE files.
2. Perform a complete [uninstall](./install_uninstalling.html) (including the -p -d uninstaller option).
3. Upgrade your OS.
4. [Install PE](/pe/latest/install_basic.html).
5. [Restore](./maintain_backup_restore.html#restore-your-database-and-puppet-enterprise-files) your backup.


## Hardware Recommendations

We provide the following hardware recommendations for Puppet Enterprise, but please note these recommendations may vary depending on the size and complexity of your PE infrastructure.

### Evaluation Environment

An evaluation environment is run on a monolithic installation and is suitable for evaluating PE on 250 or fewer nodes. For the monolithic PE install (the Puppet master, PuppetDB, and PE console roles are all on same machine), we recommend that your hardware meets the following:

Node             | Cores | RAM       | /opt/   | EC2
-----------------|------ |-----------|---------|------
Eval Monolithic node   | 4  | 6 GB   | 100 GB  | m3.large or m4.large instance

### Small Environment

A small environment is run on a monolithic installation and is suitable for running PE on 500 nodes. For the monolithic PE install (the Puppet master, PuppetDB, and PE console roles are all on same machine), we recommend that your hardware meets the following:

Node             | Cores | RAM       | /opt/   | /var/ | EC2
------------------|------|-----------|---------|-------|------
Small Monolithic node | 4  | 16 GB   | 100 GB  | 42 GB | m3.xlarge or m4.xlarge instance


### Medium Environment

A medium environment is run on a split installation and is suitable for running PE on up to 1,000 nodes. We recommend that your hardware meets the following:

Node             | Cores | RAM       | /opt/   | /var/ | EC2
------------------|------|-----------|---------|------
Puppet master   | 4      | 16 GB     | 10 GB   | 42 GB | m3.xlarge or m4.xlarge instance
PE console      | 2      | 6 GB     | 10 GB    | 22 GB | m3.large or m4.large instance
PuppetDB        | 4      | 16 GB     | 100 GB  | 42 GB | m3.xlarge or m4.xlarge instance


### Large Environment

A large environment is run on a split/large environment installation and is suitable for running PE on 1,000 or more nodes. We recommend that your hardware meets the following:

Node             | Cores | RAM       | /opt/   | /var/ | EC2
-----------------|------ |-----------|---------|------
Puppet master    | 4     | 16 GB     | 10 GB   | 42 GB | m3.xlarge or m4.xlarge instance
PE console       | 2     | 6 GB     | 10 GB    | 22 GB | m3.large or m4.large instance
PuppetDB         | 8     | 30 GB     | 200 GB  | 70 GB | m3.2xlarge instance
Compile master   | 4     | 16 GB     | 10 GB   | 42 GB | m3.xlarge or m4.xlarge instance
ActiveMQ hubs    | 2     | 4 GB      | 10 GB   | 18 GB | m3.large instance
ActiveMQ Spoke   | 2     | 4 GB      | 10 GB   | 18 GB | m3.large instance

## Supported Browsers

The following browsers are supported for use with the PE console:

* Chrome: Current version, as of release
* Firefox: Current version, as of release
* Internet Explorer: 10, and 11
* Safari: 7


## System Configuration


Before installing Puppet Enterprise at your site, you should make sure that your nodes and network are properly configured.

### Timekeeping

We recommend using NTP or an equivalent service to ensure that time is in sync between your Puppet master and any Puppet agent nodes. If time drifts out of sync in your PE infrastructure, you may encounter issues such as nodes disappearing from live manangement in the console. A service like NTP ([available as a Puppet Labs supported module](https://forge.puppetlabs.com/puppetlabs/ntp)) will ensure accurate timekeeping.

### Name Resolution

* Decide on a preferred name or set of names agent nodes can use to contact the Puppet master server.
* Ensure that the Puppet master server can be reached via domain name lookup by all of the future Puppet agent nodes at the site.

You can also simplify configuration of agent nodes by using a CNAME record to make the Puppet master reachable at the hostname `puppet`. (This is the default Puppet master hostname that is automatically suggested when installing an agent node.)

### Firewall Configuration

[mono_port_diagram]: ./images/mono_port_diagram.svg
[split_port_diagram]: ./images/split_port_diagram.svg
[lei_port_diagram]: ./images/lei_port_diagram.svg


Configure your firewalls to accommodate Puppet Enterprise's network traffic.

#### For Monolithic Installs

<a href="./images/mono_port_diagram.svg"><img src="./images/mono_port_diagram.svg" alt="Monolithic Port Diagram" title="Click to enlarge"> (Click to enlarge)</a>

<table>
  <tr>
    <th>Port</th>
    <th>Use</th>
  </tr>
  <tr>
    <td>8140</td>
    <td>
     <ul>
      <li>The Puppet master uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>The PE console sends request to the Puppet master on this port.</li>
      <li>Certificate requests are passed over this port unless ca_port is set differently.</li>
      <li>Classifier group: “PE Master”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>443</td>
    <td>
     <ul>
      <li>This port provides host access to the PE console.</li>
      <li>The PE Console accepts HTTPS traffic from end-users on this port.</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
   </td>
  </tr>
  <tr>
    <td>61613</td>
    <td>
     <ul>
      <li>MCollective uses this port to accept inbound traffic/requests from Puppet agents for orchestration.</li>
      <li>Any host used to invoke orchestration commands must be able to reach MCollective on this port.</li>
      <li>Classifier group: “PE ActiveMQ Broker”</li>
     </ul>
    </td>
  </tr>
</table>

#### For Split Installs

<a href="./images/split_port_diagram.svg"><img src="./images/split_port_diagram.svg" alt="Split Port Diagram" title="Click to enlarge"> (Click to enlarge)</a>

<table>
  <tr>
    <th>Port</th>
    <th>Use</th>
  </tr>
  <tr>
    <td>8140</td>
    <td>
     <ul>
      <li>The Puppet master uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>The PE console sends request to the Puppet master on this port.</li>
      <li>Certificate requests are passed over this port unless ca_port is set differently.</li>
      <li>Classifier group: “PE Master”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>443</td>
    <td>
     <ul>
      <li>This port provides host access to the PE console.</li>
      <li>The PE Console accepts HTTPS traffic from end-users on this port..</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
   </td>
  </tr>
  <tr>
    <td>8081</td>
    <td>
     <ul>
      <li>PuppetDB accepts traffic/requests on this port.</li>
      <li>The Puppet master and PE console send traffic to PuppetDB on this port.</li>
      <li>Classifier group: “PE PuppetDB”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>61613</td>
    <td>
     <ul>
      <li>MCollective uses this port to accept inbound traffic/requests from Puppet agents for orchestration.</li>
      <li>Any host used to invoke orchestration commands must be able to reach MCollective on this port.</li>
      <li>Classifier group: “PE ActiveMQ Broker”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>5432</td>
    <td>
     <ul>
      <li>PostgreSQL runs on this port.</li>
      <li>The PE console node will need to connect to the PuppetDB node hosting the PostgreSQL database on this port.</li>
      <li>Classifier group: “PE PuppetDB”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>4433</td>
    <td>
     <ul>
      <li>This port is used as a Classifier / Console Services API endpoint.</li>
      <li>The Puppet master needs to be able to talk to the Console over this port.</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>4435</td>
    <td>
     <ul>
      <li>This port is used as a report submission endpoint.</li>
      <li>The Puppet master communicates with the PE console over this port.</li>
      <li>Classifier group: “PE Console”</li>
      </ul>
     </td>
  </tr>
  <tr>
    <td>61616</td>
    <td>
     <ul>
      <li>This port is used for ActiveMQ hub and spoke communication.</li>
      <li>Classifier group: “PE ActiveMQ Broker”</li>
     </ul>
   </td>
  </tr>
</table>


#### For Large Environment Installations

<a href="./images/lei_port_diagram.png"><img src="./images/lei_port_diagram.png" alt="LEI Port Diagram" title="Click to enlarge"> (Click to enlarge)</a>

See the split installation port/use table for explanations of the ports and their uses.

> **Notes about Ports**:
>
>- For split and mono installs: If you are installing PE using the web-based installer, ensure port **3000** is open. You can close this port when the installation is complete. If necessary, instructions for port forwarding to the web-based installer are available in the installation instructions.
>
>- Razor uses port 8150 for HTTP and 8151 for HTTPS. Any node classified as a Razor server must be able to use these ports.

## Dependencies and OS Specific Details

This section details the packages that are installed from the various OS repos.  Unless you do not have internet access, you shouldn't need to worry about installing these manually, they will be set up during PE installation.

### PostgreSQL Requirement

If you will be using your own instance of PostgreSQL (as opposed to the instance PE can install) for the console and PuppetDB, it must be version 9.4.

### Centos

 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes
-------------|:---------:|:------------:|:-------------:|:------------------------:
pciutils     | x         |              |               |
system-logos | x         |              |               |
which        | x         |              |               |
libxml2      | x         |              |               |
dmidecode    | x         |              |               |
net-tools    | x         |              |               |
curl         |           | x            | x             |
mailcap      |           | x            | x             |
libjpeg      |           | x            |               | x
libtool-ltdl |           | x            | x             |
unixODBC     |           | x            | x             |
libxslt      |           |              |               | x
zlib         | x         |              |               |


### RHEL

 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes |
-------------|:---------:|:------------:|:-------------:|:------------------------:|
pciutils     | x         |              |               |                          |
system-logos | x         |              |               |                          |
which        | x         |              |               |                          |
libxml2      | x         |              |               |                          |
dmidecode    | x         |              |               |                          |
net-tools    | x         |              |               |                          |
cronie (RHEL 6, 7) | x      |              |               |                          |
vixie-cron (RHEL 4, 5) | x |            |               |                          |
curl         |           | x            | x             |                          |
mailcap      |           | x            | x             |                          |
libjpeg      |           | x            |               | x                        |
libtool-ltdl (RHEL 7) |  | x            | x             |                          |
unixODBC (RHEL 7) |      | x            | x             |                          |
libxslt      |           |              |               | x                        |
zlib         | x         |              |               |                          |
gtk2         |           | x            |               |                          |

### SLES

 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes |
-------------|:---------:|:------------:|:-------------:|:------------------------:|
pciutils     | x         |              |               |                          |
pmtools      | x         |              |               |                          |
cron         | x         |              |               |                          |
libxml2      | x         |              |               |                          |
net-tools    | x         |              |               |                          |
libxslt      | x         | x            |               | x                         |
curl         |           | x            | x             |                          |
libjpeg      |           | x            |               | x                        |
db43         |           | x            | x             |                          |
unixODBC     |           | x            | x             |                          |
zlib         | x         |              |               |                          |

### Ubuntu


 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes |
-------------|:---------:|:------------:|:-------------:|:------------------------:|
pciutils     | x         |              |               |                          |
dmidecode    | x         |              |               |                          |
cron         | x         |              |               |                          |
libxml2      | x         |              |               |                          |
hostname     | x         |              |               |                          |
libldap-2.4-2 | x        |              |               |                          |
libreadline5 | x         |              |               |                          |
file         |           | x            | x             |                          |
libmagic1    |           | x            | x             |                          |
libpcre3     |           | x            | x             |                          |
curl         |           | x            | x             |                          |
perl         |           | x            | x             |                          |
mime-support |           | x            | x             |                          |
libcap2      |           | x            | x             |                          |
libjpeg62    |           | x            |               | x                        |
libxslt1.1   |           |              |               | x                         |
libgtk2.0-0  |           | x            | x             | x                        |
ca-certificates-java |   | x            | x             | x                        |
openjdk-7-jre-headless* | | x            | x             | x                        |
libossp-uuid16 |         | x            | x             | x                        |
zlib         | x         |              |               |                          |

*For Ubuntu 10.04 and Debian 6, use openjdk-6-jre-headless.


### AIX

In order to run the Puppet agent on AIX systems, you must ensure the following are installed **before** attempting to install the Puppet agent:

* bash
* zlib
* readline
* curl
* OpenSSL

> **Warning**: For curl and OpenSSL, you must use the versions provided by the "AIX Toolbox Cryptographic Content" repository, which is available via IBM support. Note that the curl version must be 7.9.3. Do not use the curl version in the AIX toolbox package for Linux applications, as that version does not include support for OpenSSL.

To install the bash, zlib, and readline packages on your selected node directly, you can run `rpm -Uvh` with the following URLs (note that the RPM package provider on AIX must be run as root):

 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/bash/bash-3.2-1.aix5.2.ppc.rpm
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/zlib/zlib-1.2.3-4.aix5.2.ppc.rpm
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-6.1-1.aix6.1.ppc.rpm (AIX 6.1 and 7.1 *only*)
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-4.3-2.aix5.1.ppc.rpm (AIX 5.3 *only*)

If you are behind a firewall or running an http proxy, the above commands may not work. Instead, use the [AIX toolbox packages](http://www-03.ibm.com/systems/power/software/aix/linux/toolbox/alpha.html) download available from IBM.

GPG verification will not work on AIX, the RPM version used by AIX (even 7.1) is too old. The AIX package provider doesn't support package downgrades (installing an older package over a newer package). Avoid using leading zeros when specifying a version number for the AIX provider (i.e., use `2.3.4` not `02.03.04`).

The PE AIX implementation supports the NIM, BFF, and RPM package providers. Check the [Type Reference](/references/4.2.latest/type.html#package) for technical details on these providers.

### Solaris

Solaris support is agent only.

For Solaris 10, the following packages are required:

  * SUNWgccruntime
  * SUNWzlib
  * In some instances, bash may not be present on Solaris systems. It needs to be installed before running the PE installer. Install it via the media used to install the OS or via CSW if that is present on your system. (CSWbash or SUNWbash are both suitable.)

For Solaris 11 the following packages are required:

  * system/readline
  * system/library/gcc-45-runtime
  * library/security/openssl

These packages are available in the Oracle Solaris release repository (enabled by default on Solaris 11). The PE installer will automatically install them; however, if the release repository is not enabled, the packages will need to be installed manually.

* * *

Next Steps

* To install Puppet Enterprise on \*nix nodes, continue to [Installing Puppet Enterprise](./install_basic.html).
* To install Puppet Enterprise on Windows nodes, continue to [Installing Windows Agents](./install_windows.html).
* To install Puppet Enterprise on OS X, continue to [Installing Mac OS X Agents](./install_osx.html).
