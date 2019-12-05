---
layout: default
title: "Supported platforms known issues"
canonical: "/pe/latest/release_notes_known_issues_platforms.html"
---

This page lists known issues for platforms supported in Puppet Enterprise.

## **New**: SE Linux Puppet resources can't be managed in 2016.2.1

If you're running RHEL 6 or 7 agents, you can't manage SE Linux Puppet resources with Puppet agent 1.5.3 (included in PE 2016.2.1). This will be fixed in a future release.

<!--RE-7660-->

## PXP agent stopped after upgrading Solaris agents

After upgrading a Solaris agent, the PXP agent service may not properly restart. To restart the Puppet agent, run Puppet again or wait until the next timed run. 

<!--PE-13918-->

## Ubuntu conflict when you're running on Fusion VM and Amazon EC2

We’ve discovered an issue in which customers on some Ubuntu 14.04 amd64 platforms, such as on a Fusion VM or in Amazon EC2 cannot properly install or upgrade to PE 2015.2. Depending on your situation, the current workarounds are available:

- [You've not yet attempted to install or upgrade](#not-yet-attempted-to-install-or-upgrade).
- [You've already attempted a fresh install and failed](#attempted-a-fresh-install-and-failed).
- [You've already attempted to upgrade and have a system backup](#attempted-to-upgrade-and-have-a-system-backup).
- [You've already attempted to upgrade and do not have a system backup](#attempted-to-upgrade-and-do-not-have-a-system-backup).

### Not yet attempted to install or upgrade

You will need to install the libc6-dev package on the machine you've assigned the Puppet master component.

1. On the machine assigned the Puppet master component, install the libc6-dev package.

   Run `sudo apt-get install --no-install-recommends libc6-dev`.

2. Follow the [PE installation process](./install_basic.html) or [upgrade process](./upgrade_mono.html) as normal.

### Attempted a fresh install and failed

If you have attempted a brand new install and are in a broken state, perform the following steps:

1. [Uninstall PE](./install_uninstalling.html).

   From the directory where you unpacked the Puppet Enterprise tarball, run `sudo ./puppet-enterprise-uninstall -pd`.

2. On the machine assigned the Puppet master component, install the libc6-dev package.

   Run `sudo apt-get install --no-install-recommends libc6-dev`.

3. Follow the [PE installation process](./install_basic.html) as normal.

### Attempted to upgrade and have a system backup

If you have already attempted to upgrade, are now in a broken state and have a backup, perform the following steps:

1. Restore your backup.
2. On the machine assigned the Puppet master component, install the libc6-dev package.

   Run `sudo apt-get install --no-install-recommends libc6-dev`.

3. Run the upgrade again.

### Attempted to upgrade and do not have a system backup

If you have already attempted to upgrade, are now in a broken state and **do not have a backup**:

1. On the machine assigned the Puppet master component, install the libc6-dev package.

   Run `sudo apt-get install --no-install-recommends libc6-dev`.

2. Still on the Puppet master, change your PE version to the version number you were upgrading from.

   Run `sudo sh -c 'echo "<VERSION UPGRADING FROM>" > /opt/puppet/pe_version'`

   Note that the PE installer script has a check to ensure we do not attempt to upgrade/install if the same version is already installed.

3. Run the upgrade again.

## Readline version issues on AIX agents

- AIX 5.3 Puppet agents depend on readline-4-3.2 being installed. You can check the installed version of readline by running `rpm -q readline`. If you need to install it, you can [download it from IBM](ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-4.3-2.aix5.1.ppc.rpm). Install it *before* installing the Puppet agent.

- On AIX 6.1 and 7.1, the default version of readline, 4-3.2, is insufficient. You need to replace it *before* upgrading or installing by running

        rpm -e --nodeps readline
        rpm -Uvh readline-6.1-1.aix6.1.ppc.rpm

If you see an error message after running this, you can disregard it. Readline-6 should be successfully installed, and you can proceed with the installation or upgrade (you can verify the installation with  `rpm -q readline`).

## Solaris updates might break Puppet install

We've seen an issue in which Puppet agents on Solaris platforms have quit responding after the core Solaris OS was updated. Essential Puppet configuration files were erased from the `/etc/` directory (which includes SSL certs needed to communicate with the Puppet master), and the `/etc/hosts` file was reverted.

If you encounter this issue, log in to the Puppet master and clear the agent cert from the Solaris machine (`puppet cert clean <HOSTNAME>`), and then re-install [the Puppet agent](.//install_agents.html).

## Solaris 10 and 11 have no default `symlink` directory

Solaris 10 and 11 will not by default have the `symlink` directory in their path. Therefore, if you use one of these two platforms,  add `/usr/local/bin` to your default path.

## Debian/Ubuntu local hostname issue

On some versions of Debian/Ubuntu, the default `/etc/hosts` file contains an entry for the machine's hostname with a local IP address of 127.0.1.1. This can cause issues for PuppetDB and PostgreSQL, because binding a service to the hostname causes it to resolve to the local-only IP address rather than its public IP. As a result, nodes (including the console) will fail to connect to PuppetDB and PostgreSQL.

To fix this, add an entry to `/etc/hosts` that resolves the machine's FQDN to its *public* IP address. This should be done prior to installing PE. However, if PE has already been installed, restarting the `pe-puppetdb` and `pe-postgresql` services after adding the entry to the hosts file should fix things.

## Puppet Enterprise cannot locate Samba init script for Ubuntu 14.04

If you attempt to install and start Samba using PE resource management, you might encounter the following errors:

    Error: /Service[smb]: Could not evaluate: Could not find init script or upstart conf file for 'smb'`
    Error: Could not run: Could not find init script or upstart conf file for 'smb'`

To work around this issue, install and start Samba with the following commands:

    puppet resource service smbd provider=init enable=true ensure=running
    puppet resource service nmbd provider=init enable=true ensure=running

Or, add the following to a Puppet manifest:

	service { 'smbd':
  		ensure   => running,
  		enable   => true,
  		provider => 'init',
	}

	service { 'nmbd':
  		ensure   => running,
  		enable   => true,
  		provider => 'init',
	}


## Augeas file access issue

On AIX agents, the Augeas lens is unable to access or modify `/etc/services`. There is no known workaround.

## Puppet expands variables in Windows systems path

Puppet will automatically expand variables in a system path.

For example, this path:

	PATH=%SystemRoot%\System32

Will be expanded, as follows:

	PATH=C:\Windows\System32

This should not cause any problems.

## A note about unprivileged non-admin agent actions on Windows and VirtualStore

This applies if:

- You run a PE agent on Windows with non-admin privileges and attempt to create a file without the correct access.

- You run a PE agent on Windows with non-admin privileges and attempt to create a registry key.

If you run the puppet agent on Windows without admin privileges, you are subject to Microsoft's file and registry virtualization. Essentially, this means that if you create any Puppet agent reporting resources (e.g., files or registry keys) in privileged locations, Windows redirects them to the current user's VirtualStore.
