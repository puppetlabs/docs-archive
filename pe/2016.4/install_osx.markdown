---
layout: default
title: "Installing Mac OS X agents"
canonical: "/pe/latest/install_osx.html"
---

Mac OS X agents provide consistent automated management of Apple laptops and desktops from the same Puppet Enterprise infrastructure that manages your servers. 

Capabilities include PE core functionality, plus OS X-specific capabilities:

* Package installation via DMG and PKG
* Service management via LaunchD
* Directory Services integration for local user/group management
* Inventory facts via System Profiler

## Install OS X agents

Mac OS X agents can be installed with PE package management, from Finder, or from the command line.

See the [system requirements](./sys_req_os.html#puppet-agent-platforms) for which Mac OS X versions PE supports.

Refer to [Puppet Enterprise binaries and symlinks](./install_pe_mono.html#puppet-enterprise-binaries-and-symlinks) for information about the binaries and symlinks PE sets. You can also find instructions on disabling the symlinks.

Before you begin, download the [OS X PE agent package](http://puppetlabs.com/download-puppet-enterprise) and transfer it to the machine you're installing the agent on.
 
### Change Mac OS X name to lowercase letters

Before you install a Puppet agent on your OS X machine, you need to ensure the name of your machine uses lowercase letters.

In PE, agent certnames need to be lowercase. For Mac OS X agents, the certname is derived from the name of the machine (e.g., My-Example-Mac). To prevent installation issues, you need to make sure the name of your machine uses lowercases letters.

>**Note:** If you don't want to change your computer's name, you can also enter the agent certname in all lowercase letters when prompted by the installer.

1. To change the name to use lowercase letters, choose one of the following:

   - In **System Preferences**, select **Sharing** > **Computer Name** > **Edit**.
   
   - From the command line, run the following commands:
   
     1. `sudo scutil --set ComputerName <new machine name>`
     2. `sudo scutil --set LocalHostName <new machine name>`
     3. `sudo scutil --set HostName <new machine name>`

### Install with Puppet Enterprise package management

PE provides its own package management to help you install Puppet agents in your PE infrastructure.

To install a Mac OS X agent using PE package management, refer to [Installing Puppet agents with PE package management](/install_agents.html#install-agents-with-a-different-os-and-architecture-than-the-puppet-master).

### Install from Finder

You can use Finder to install the agent on your Mac OS X machine. 

1. Open the OS X PE agent package .dmg and click the installer .pkg.
2. Follow the instructions in the installer dialog. You will need to include the Puppet master's hostname and the agent's certname.

   >**Tip:** You can get the Puppet master's hostname with `puppet cert list --all`.

The installer generates a certificate signing request you must [sign](./install_agents.html#managing-agent-certificate-requests).

### Install from the command line

You can use the command line to install the agent on your Mac OS X machine. 

1. SSH into your OS X node as root or a sudo user. Note that you will be in `/var/root`.
2. Run `sudo hdiutil mount <DMGFILE>`.

   You should see a line that ends, `/Volumes/puppet-agent-VERSION`. This is the mount point for the virtual volume created from the disk image.

4. Run `cd /Volumes/puppet-agent-VERSION`.
5. Run `sudo installer -pkg puppet-agent-installer.pkg -target /`.
6. To verify the install, run `/opt/puppetlabs/bin/puppet --version`.

   >**Tip**: Run `PATH=/opt/puppetlabs/bin:$PATH;export PATH` to add the PE binaries to your path.

7. Using the instructions in [configuring agents](./install_agents.html#configuring-agents), point the OS X agent at the correct Puppet master and set the agent's certname.
8. Kick off a Puppet run using `sudo puppet agent -t`. This generates a certificate signing request you must [sign](./install_agents.html#managing-agent-certificate-requests).
