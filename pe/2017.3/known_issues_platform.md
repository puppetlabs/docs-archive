# Supported platforms known issues

This page lists known issues for platforms supported in Puppet Enterprise.

## Ubuntu 16.10 agents can't be installed with package management

Installation using package management isn't currently supported. If you need to install an Ubuntu 16.10 agent, log into the node where you want to install the agent and run:

```
wget https://apt.puppetlabs.com/puppetlabs-release-pc1-yakkety.deb
sudo dpkg -i puppetlabs-release-pc1-yakkety.deb
```

## PXP agent stops after upgrading Solaris agents

After upgrading a Solaris agent, the PXP agent service might not restart properly. To restart the agent, run Puppet again or wait until the next timed run.

## Readline version issues on AIX agents

-   AIX 5.3 agents depend on readline-4-3.2. You can check the installed version of readline by running `rpm -q readline`. If you need to install it, you can download it from IBM. Install it before installing the agents.

-   On AIX 6.1 and 7.1, the default version of readline, 4-3.2, is insufficient. You must replace it before upgrading or installing by running:

```
rpm -e --nodeps readline
rpm -Uvh readline-6.1-1.aix6.1.ppc.rpm
```

    If you see an error message after installing readline, you can disregard it.


## Solaris updates might break Puppet install

In some cases, agents on Solaris platforms quit responding after the core Solaris OS was updated. Essential configuration files were erased from the `/etc/` directory \(which includes SSL certs needed to communicate with the master\), and the `/etc/hosts` file was reverted.

If you encounter this issue, log in to the master and clear the agent cert from the Solaris machine \(`puppet cert clean <HOSTNAME>`\), and then re-install the agent.

## Solaris 10 and 11 have no default symlink directory

Solaris 10 and 11 don't have the `symlink` directory in their path by default. If you use one of these two platforms, add `/usr/local/bin` to your default path.

## Debian and Ubuntu local hostname issue

On some versions of Debian and Ubuntu, the default `/etc/hosts` file contains an entry for the machine's hostname with a local IP address of 127.0.1.1. This can cause issues for PuppetDB and PostgreSQL, because binding a service to the hostname causes it to resolve to the local-only IP address rather than its public IP. As a result, nodes \(including the console\) fail to connect to PuppetDB and PostgreSQL.

To fix this, add an entry to `/etc/hosts` that resolves the machine's FQDN to its *public* IP address. Complete this step before installing PE. If PE has already been installed, restart or reload the `pe-puppetdb` and `pe-postgresql` services after adding the entry to the hosts file.

## PE can't locate Samba init script for Ubuntu 14.04

If you attempt to install and start Samba using resource management, you might encounter these errors:

```
Error: /Service[smb]: Could not evaluate: Could not find init script or upstart conf file for 'smb'`
Error: Could not run: Could not find init script or upstart conf file for 'smb'`
```

To work around this issue, install and start Samba with these commands:

```
puppet resource service smbd provider=init enable=true ensure=running
puppet resource service nmbd provider=init enable=true ensure=running
```

Or, add the Samba service to a manifest:

```
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
```

## Augeas file access issue

On AIX agents, the Augeas lens is unable to access or modify `/etc/services`. There is no known workaround.

## Variables are expanded in Windows systems path

Puppet automatically expands variables in a system path.

For example, this path:

```
PATH=%SystemRoot%\System32
```

Is expanded to:

```
PATH=C:\Windows\System32
```

This should not cause any problems.

