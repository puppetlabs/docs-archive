# Supported operating systems

Puppet Enterprise supports various operating systems depending on the role a machine assumes in your infrastructure.

## Supported operating systems and devices

When choosing an operating system, first consider the machine's role. Different roles support different operating systems and architectures.

**Tip:** For details about platform support lifecycles and planned end-of-life support, see [Platform support lifecycle](https://puppet.com/docs/puppet-enterprise/platform-support-lifecycle/) on the Puppet website.

### Master platforms

The master role can be installed on these operating systems and architectures.

|Operating system|Versions|Architecture|
|----------------|--------|------------|
|Enterprise Linux -   CentOS

-   Oracle Linux

-   Red Hat Enterprise Linux

-   Scientific Linux


|6, 7, 8|-   x86\_64
-   FIPS 140-2 compliant version 7

|
|SUSE Linux Enterprise Server|12|x86\_64|
|Ubuntu \(General Availability kernels\)|16.04, 18.04|amd64|

### Agent platforms

The agent role can be installed on these operating systems and architectures.

|Operating system|Versions|Architecture|
|----------------|--------|------------|
|AIX|7.1, 7.2**Note:** We support only technology levels that are still under support from IBM.

||
|Amazon Linux|2||
|Debian|Jessie \(8\), Stretch \(9\), Buster \(10\)|-   i386

-   amd64


|
|Enterprise Linux -   CentOS

-   Oracle Linux

-   Red Hat Enterprise Linux
-   Scientific Linux

|5, 6, 7, 8

 **Note:** Scientific Linux 5 is not supported.

|-   x86\_64

-   i386 for versions 5, 6

-   ppc64le for version 7

-   aarch64 for version 7

-   FIPS 140-2 compliant version 7


|
|Fedora|28, 29, 30, 31|-   x86\_64

-   i386 for version 25


|
|macOS|10.12, 10.13, 10.14| |
|Microsoft Windows|7, 8.1, 10|-   x64

-   x86

-   FIPS 140-2 compliant version 10

|
|Microsoft Windows Server|2008, 2008 R2, 2012, 2012 R2, 2012 R2 core, 2016, 2016 core, and Desktop Experience, 2019|-   x64 for all 2008 and 2012 series

-   x86 for 2008

-   FIPS 140-2 compliant versions 2012R2 and 2012R2 core

|
|Solaris|10, 11|-   SPARC

-   i386


|
|SUSE Linux Enterprise Server|11, 12, 15|-   x86\_64

-   i386 for version 11

-   ppc64le for version 12


|
|Ubuntu \(General Availability kernels\)|16.04, 18.04, 20.04|-   amd64

-   i386 for versions 16.04

-   ppc64el for version 16.04


|

**Note:** Some operating systems require an active subscription with the vendor's package management system \(for example, the Red Hat Network\) to install dependencies.

## CentOS dependencies

When you install PE or an agent, these packages are also installed from the various operating system repositories.

If the machine you're installing on has internet access, dependencies are set up during installation. If your machine doesn't have internet access, you must manually install dependencies.

| |All Nodes|Master Nodes|Console Nodes|Console/Console DB Nodes|
|--|:-------:|:----------:|:-----------:|------------------------|
|pciutils|x| | | |
|which|x| | | |
|libxml2|x| | | |
|dmidecode|x| | | |
|net-tools|x| | | |
|curl| |x|x| |
|mailcap| |x|x| |
|libjpeg| |x| |x|
|libtool-ltdl| |x|x| |
|unixODBC| |x|x| |
|libxslt| | | |x|
|zlib|x| | | |

## RHEL dependencies

When you install PE or an agent, these packages are also installed from the various operating system repositories.

If the machine you're installing on has internet access, dependencies are set up during installation. If your machine doesn't have internet access, you must manually install dependencies.

| |All Nodes|Master Nodes|Console Nodes|Console/Console DB Nodes|
|--|:-------:|:----------:|:-----------:|:----------------------:|
|pciutils|x| | | |
|which|x| | | |
|libxml2|x| | | |
|dmidecode|x| | | |
|net-tools|x| | | |
|cronie \(RHEL 6, 7\)|x| | | |
|vixie-cron \(RHEL 4, 5\)|x| | | |
|curl| |x|x| |
|mailcap| |x|x| |
|libjpeg| |x| |x|
|libtool-ltdl \(RHEL 7\)| |x|x| |
|unixODBC \(RHEL 7\)| |x|x| |
|libxslt| | | |x|
|zlib|x| | | |
|gtk2| |x| | |

## SUSE Linux Enterprise Server dependencies

When you install PE or an agent, these packages are also installed from the various operating system repositories.

If the machine you're installing on has internet access, most dependencies are installed during installation. If you encounter problems, inspect the error messages for packages that require other SUSE Linux Enterprise Server packaging modules to be enabled, and use `zypper package-search <PACKAGE NAME>` to locate them for manual installation.

| |All Nodes|Master Nodes|Console Nodes|Console/Console DB Nodes|
|--|:-------:|:----------:|:-----------:|:----------------------:|
|pciutils|x| | | |
|pmtools|x| | | |
|cron|x| | | |
|libxml2|x| | | |
|net-tools|x| | | |
|libxslt|x|x| |x|
|curl| |x|x| |
|libjpeg| |x| |x|
|db43| |x|x| |
|unixODBC| |x|x| |
|zlib|x| | | |
|libboost\_atomic|x| | | |
|libboost\_chrono|x| | | |
|libboost\_date\_time|x| | | |
|libboost\_filesystem|x| | | |
|libboost\_locale|x| | | |
|libboost\_log|x| | | |
|libboost\_program\_options|x| | | |
|libboost\_random|x| | | |
|libboost\_regex|x| | | |
|libyaml-cpp|x| | | |

## Ubuntu dependencies

When you install PE or an agent, these packages are also installed from the various operating system repositories.

If the machine you're installing on has internet access, dependencies are set up during installation. If your machine doesn't have internet access, you must manually install dependencies.

| |All Nodes|Master Nodes|Console Nodes|Console/Console DB Nodes|
|--|:-------:|:----------:|:-----------:|:----------------------:|
|pciutils|x| | | |
|dmidecode|x| | | |
|cron|x| | | |
|libxml2|x| | | |
|hostname|x| | | |
|libldap-2.4-2|x| | | |
|libreadline5|x| | | |
|file| |x|x| |
|libmagic1| |x|x| |
|libpcre3| |x|x| |
|curl| |x|x| |
|perl| |x|x| |
|mime-support| |x|x| |
|libcap2| |x|x| |
|libjpeg62| |x| |x|
|libxslt1.1| | | |x|
|libgtk2.0-0| |x|x|x|
|ca-certificates-java| |x|x|x|
|openjdk-7-jre-headless\*| |x|x|x|
|libossp-uuid16| |x|x|x|
|zlib|x| | | |

\*For Ubuntu 18.04, use openjdk-8-jre-headless. This package requires the [universe repository](https://help.ubuntu.com/community/Repositories/Ubuntu) to install.

## AIX dependencies and limitations

Before installing the agent on AIX systems, install these packages.

-   bash
-   zlib
-   readline
-   curl
-   OpenSSL

CAUTION:

For cURL and OpenSSL, you must use the versions provided by the "AIX Toolbox Cryptographic Content" repository, which is available via IBM support. Note that the cURL version must be 7.9.3. Do not use the cURL version in the AIX toolbox package for Linux applications, as that version does not include support for OpenSSL.

To install the bash, zlib, and readline packages on a node directly, run `rpm -Uvh` with the following URLs. The RPM package provider must be run as root.

-   [ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/bash/bash-3.2-1.aix5.2.ppc.rpm](ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/bash/bash-3.2-1.aix5.2.ppc.rpm)
-   [ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/zlib/zlib-1.2.3-4.aix5.2.ppc.rpm](ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/zlib/zlib-1.2.3-4.aix5.2.ppc.rpm)
-   [ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-6.1-1.aix6.1.ppc.rpm](ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-6.1-1.aix6.1.ppc.rpm) \(AIX 6.1 and 7.1 only\)

-   [ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-4.3-2.aix5.1.ppc.rpm](ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-4.3-2.aix5.1.ppc.rpm) \(AIX 5.3 only\)


If you are behind a firewall or running an http proxy, the above commands might not work. Instead, use the AIX toolbox packages download available from IBM.

GPG verification does not work on AIX, because the RPM version it uses is too old. The AIX package provider doesn't support package downgrades \(installing an older package over a newer package\). Avoid using leading zeros when specifying a version number for the AIX provide, for example, use `2.3.4` not `02.03.04`.

The PE AIX implementation supports the NIM, BFF, and RPM package providers. Check the type reference for technical details on these providers.

## Solaris dependencies and limitations

Solaris support is agent only.

For Solaris 10, these packages are required:

-   SUNWgccruntime
-   SUNWzlib
-   In some instances, bash might not be present on Solaris systems. It needs to be installed before running the installer. Install bash via the media used to install the operating system, or via CSW if that is present on your system. \(CSWbash or SUNWbash are both suitable.\)

For Solaris 11 these packages are required:

-   system/readline
-   system/library/gcc-45-runtime
-   library/security/openssl

These packages are available in the Solaris release repository, which is enabled by default in version 11. The installer automatically installs these packages; however, if the release repository is not enabled, the packages must be installed manually.

## Upgrade your operating system with PE installed

If you have PE installed, take extra precautions before performing a major upgrade of your machine's operating system.

Performing major upgrades of your operating system with PE installed can cause errors and issues with PE. A major operating system upgrade is an upgrade to a new whole version, such as an upgrade from CentOS 6.0 to 7.0; it does not refer to a minor version upgrade, like CentOS 6.5 to 6.6. Major upgrades typically require a new version of PE.

1.  Back up your databases and other PE files.

2.  Perform a complete uninstall \(using the `-p` and `-d` uninstaller options\).

3.  Upgrade your operating system.

4.  Install PE.

5.  Restore your backup.


**Related information**  


[Back up your infrastructure](backing_up_and_restoring_pe.md#)

[Restore your infrastructure](backing_up_and_restoring_pe.md#)

[Uninstalling](uninstalling.md#)

[Installing Puppet Enterprise](installing_pe.md#)

