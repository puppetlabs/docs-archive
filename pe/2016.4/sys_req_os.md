---
layout: default
title: "Supported operating systems"
canonical: "/pe/latest/sys_req_os.html"
---


## Supported operating systems

Puppet Enterprise runs on the following platforms:

### Puppet master platforms

<table>
  <tr>
    <th>Operating system</th>
    <th>Versions</th>
    <th>Architecture</th>
  </tr>
  <tr>
    <td>Red Hat Enterprise Linux</td>
    <td>6, 7</td>
    <td>x86_64</td>
  </tr>
  <tr>
    <td>CentOS</td>
    <td>6, 7</td>
    <td>x86_64</td>
   </tr>
  <tr>
    <td>Oracle Linux</td>
    <td>6, 7</td>
    <td>x86_64</td>
   </tr>
  <tr>
    <td>Scientific Linux</td>
    <td>6, 7</td>
    <td>x86_64</td>
   </tr>
  <tr>
    <td>SUSE Linux Enterprise Server</td>
    <td>11 (SP1/SP2), 12 (SP1/SP2)</td>
    <td>x86_64</td>
   </tr>
   <tr>
    <td>Ubuntu</td>
    <td>14.04, 16.04</td>
    <td>x86_64</td>
   </tr>
</table>

### Puppet agent platforms

<table>
  <tr>
    <th>Operating system</th>
    <th>Versions</th>
    <th>Architecture</th>
  </tr>
  <tr>
    <td>Red Hat Enterprise Linux</td>
    <td>5, 6, 7</td>
    <td>
     <ul>
      <li>x86_64</li>
      <li>i386 for 5, 6</li>
      <li>ppc64le for 7</li>
      <li>aarch64 for 7</li>
      <li>IBM z System for 6, 7 (<a href="https://puppet.com/product/pricing">contact sales</a>)</li>

     </ul>
    </td>
  </tr>
  <tr>
    <td>CentOS</td>
    <td>5, 6, 7</td>
    <td>
     <ul>
      <li>x86_64</li>
      <li>i386 for 5, 6</li>
     </ul>
    </td>
   </tr>
  <tr>
    <td>Oracle Linux</td>
    <td>5, 6, 7</td>
    <td>
     <ul>
      <li>x86_64</li>
      <li>i386 for 5, 6</li>
     </ul>
    </td>
   </tr>
  <tr>
    <td>Scientific Linux</td>
    <td>5, 6, 7</td>
    <td>
     <ul>
      <li>x86_64</li>
      <li>i386 for 5, 6</li>
     </ul>
    </td>
   </tr>
  <tr>
    <td>SUSE Linux Enterprise Server</td>
    <td>11 (SP1/SP2), 12 (SP1/SP2)</td>
    <td>
     <ul>
      <li>x86_64</li>
      <li>i386 for 11</li>
      <li>ppc64le for 12</li>
      <li>IBM z System for 11, 12 (<a href="https://puppet.com/product/pricing">contact sales</a>)</li>
     </ul>
    </td>
   </tr>
  <tr>
    <td>Solaris</td>
    <td>10 (update 9 or later), 11</td>
    <td>
      <ul>
        <li>SPARC</li>
        <li>i386</li>
       </ul>
     </td>
   </tr>
  <tr>
    <td>Ubuntu</td>
    <td>14.04, 16.04</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
        <li>ppc64le for 16.04</li>
       </ul>
     </td>
   </tr>
  <tr>
    <td>Fedora</td>
    <td>25, 26</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
       </ul>
     </td>
   </tr>
  <tr>
    <td>Debian</td>
    <td>Wheezy (7), Jessie (8), Stretch (9)</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
       </ul>
     </td>
   </tr>
  <tr>
    <td>Microsoft Windows (Server OS)</td>
    <td>2008, 2008R2, 2012, 2012R2, 2012R2 core, 2016 (Core, and with Desktop Experience)</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
       </ul>
     </td>
   </tr>
  <tr>
    <td>Microsoft Windows (Consumer OS)</td>
    <td>Vista, 7, 8.1, 10</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
       </ul>
     </td>
   </tr>
  <tr>
    <td>Mac</td>
    <td>10.10, 10.11, 10.12, 10.13</td>
    <td style="text-align:center">x86_64</td>
   </tr>
  <tr>
    <td>AIX</td>
    <td>6.1, 7.1, 7.2</td>
    <td style="text-align:center">Power</td>
   </tr>
</table>

*contact sales for pricing info

>**Note**: Some operating systems require an active subscription with the vendor's package management system (e.g., the Red Hat Network) to install dependencies.


### Supported network devices

Puppet Enterprise supports running Puppet agents on the following networking devices/operating systems:

- **Arista EOS**: Refer the [aristanetworks/netdev_stdlib_eos module](https://forge.puppetlabs.com/aristanetworks/netdev_stdlib_eos#limitations) on the Puppet Forge for system requirements and dependencies.
- **Cumulus Linux**: Refer to the [Cumulus Linux page](https://forge.puppetlabs.com/cumuluslinux/) on the Puppet Forge for system requirements and dependencies.
- **Cisco NS-OX EOS**: Refer to the [Cisco Puppet page](https://forge.puppet.com/puppetlabs/ciscopuppet/readme) on the Puppet Forge for system requirements and dependencies.
- **Cisco IOS-XR**: Refer to the [Cisco Puppet page](https://forge.puppet.com/puppetlabs/ciscopuppet) on the Puppet Forge for system requirements and dependencies.
- **Huawei CloudEngine**: Refer to [Installing Huawei CloudEngine Agents](./install_huawei_ce.html) for installation instructions.

## Operating system support life cycles

### Puppet agent and operating system support life cycles

Puppet ends platform-specific support for Puppet agent at the platform vendor’s end-of-life (EOL) date.

For example, Fedora 20 reached its EOL on June 23, 2015. As of that date, Puppet stopped testing and providing fixes, updates, and support for Puppet Enterprise and open source Puppet versions of that agent.

On platforms where the vendor does not publish EOL dates, we support Puppet agent on the two most recent releases of a platform (e.g Mac OS X 10.10 and 10.11). (When Mac OS 10.12 launches, we will send a notice of the deprecation plan for Mac OS X 10.10).

Puppet may choose to continue to support certain agent operating systems after the platform vendor’s EOL solely at our discretion.

In all cases, we will announce a Puppet agent platform’s EOL at least 90 days before Puppet’s EOL date for the platform.

### PE master and operating system support life cycles

Puppet aligns Puppet Enterprise master platform support to the [PE version support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle).
In general, Puppet supports a Puppet Enterprise master platform until the [EOL date](https://puppet.com/misc/puppet-enterprise-lifecycle) for a Puppet Enterprise version.

However, if a PE master platform’s vendor EOL date is more than 6 months sooner than its PE Version’s EOL date, we may, at our discretion, end support for that master platform at the platform vendor’s EOL date.

In all cases, we will announce a PE master platform’s EOL at least 6 months before Puppet’s EOL date for the platform.

### Upcoming platform end-of-life (EOL)

In accordance with our operating system [support lifecycle policy](https://puppet.com/misc/puppet-enterprise-lifecycle), platform-specific support will end on the specified EOL dates, as listed below.

At EOL, Puppet will stop providing agents, masters, patches, fixes and security updates for the operating system.

<table>
 <tr>
   <th>Operating system</th>
   <th>PE agent and/or master</th>
   <th>OS vendor's EOL</th>
   <th>Puppet's EOL</th>
  </tr>
  <tr>
   <td>Fedora 22</td>
   <td>Agent</td>
   <td>7/19/2016</td>
   <td>11/30/2016</td>
  </tr>
  <tr>
   <td>Mac OS X 10.9</td>
   <td>Agent</td>
   <td></td>
   <td>11/30/2016</td>
  </tr>
  <tr>
   <td>Ubuntu 15.10 (Wily)</td>
   <td>Agent</td>
   <td>7/28/2016</td>
   <td>11/30/2016</td>
  </tr>
  <tr>
   <td>Ubuntu 10.04 (Lucid)</td>
   <td>Agent</td>
   <td>4/30/2015</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>Ubuntu 12.04 (Precise)</td>
   <td>Agent, Master</td>
   <td>April 2017</td>
   <td>4/30/2017</td>
  </tr>
  <tr>
   <td>Windows Server 2003</td>
   <td>Agent for 3.8 (not >=2015.2)</td>
   <td>7/14/2015</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>Debian 6 (Squeeze)</td>
   <td>Agent and Master for 3.8 (not >=2015.2)</td>
   <td>2/29/2016</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>SLES 10</td>
   <td>Agent</td>
   <td>7/31/2016</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>EL (RHEL, CentOS, Scientific Linux, Oracle Linux) 4</td>
   <td>Agent</td>
   <td>3/31/2017</td>
   <td>3/31/2017</td>
  </tr>
  <tr>
   <td>AIX 5.3</td>
   <td>Agent</td>
   <td>4/30/2012</td>
   <td>6/30/2017</td>
  </tr>
 </table>



## Performing major OS upgrades when Puppet Enterprise is installed

In this context, a major OS upgrade refers to an upgrade to a new whole version, such as an upgrade from CentOS 6.0 to CentOS 7.0; it does not refer to a minor version upgrade (e.g., CentOS 6.5 to CentOS 6.6). A major OS upgrade typically requires [a new version of PE](https://puppetlabs.com/misc/pe-files).

Performing major upgrades of your OS while PE is installed can cause problems with PE. To perform an OS upgrade, you’ll need to perform the following steps:

1. [Back up](./maintain_backup_restore.html#back-up-your-database-and-puppet-enterprise-files) your databases and other PE files.
2. Perform a complete [uninstall](./install_uninstalling.html) (including the -p -d uninstaller option).
3. Upgrade your OS.
4. [Install PE](/pe/latest/install_basic.html).
5. [Restore](./maintain_backup_restore.html#restore-your-database-and-puppet-enterprise-files) your backup.

## Dependencies and OS-specific details

These sections show which OS packages are installed from the various OS repos. If the machine you're installing on has internet access, they will be set up during PE installation. If it doesn't, you must install them manually.

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

**Before installing the Puppet agent on AIX systems,** install the following packages on AIX systems.

These packages are required to **install and run** the Puppet agent on AIX systems.

* bash
* zlib
* readline
* curl
* OpenSSL

> **Warning**: For cURL and OpenSSL, you must use the versions provided by the "AIX Toolbox Cryptographic Content" repository, which is available via IBM support. Note that the cURL version must be 7.9.3. Do not use the cURL version in the AIX toolbox package for Linux applications, as that version does not include support for OpenSSL.

To install the bash, zlib, and readline packages on your selected node directly, you can run `rpm -Uvh` with the following URLs (note that the RPM package provider on AIX must be run as root):

 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/bash/bash-3.2-1.aix5.2.ppc.rpm
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/zlib/zlib-1.2.3-4.aix5.2.ppc.rpm
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-6.1-1.aix6.1.ppc.rpm (AIX 6.1 and 7.1 *only*)
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-4.3-2.aix5.1.ppc.rpm (AIX 5.3 *only*)

If you are behind a firewall or running an http proxy, the above commands may not work. Instead, use the [AIX toolbox packages](http://www-03.ibm.com/systems/power/software/aix/linux/toolbox/alpha.html) download available from IBM.

GPG verification will not work on AIX, the RPM version used by AIX (even 7.1) is too old. The AIX package provider doesn't support package downgrades (installing an older package over a newer package). Avoid using leading zeros when specifying a version number for the AIX provider (i.e., use `2.3.4` not `02.03.04`).

The PE AIX implementation supports the NIM, BFF, and RPM package providers. Check the [type reference]({{puppet}}/type.html#package) for technical details on these providers.

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
