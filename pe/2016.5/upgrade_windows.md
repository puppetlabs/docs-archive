---
layout: default
title: "Upgrading Windows Agents"
canonical: "/pe/latest/install_windows.html"
---

You should upgrade your Puppet Enterprise Windows agents as new releases become available. 

## Upgrade Windows agents

When upgrading your Puppet agents, you can use, the puppetlabs-puppet_agent module, PE package management or the Windows agent .msi package. 

Before you begin upgrading your agents, first upgrade your [monolithic](./upgrade_mono.html) or [split](./upgrade_split.html) Puppet master.

>**Caution:** Upgrading Windows agents can cause non-Puppet-related network services to restart.

### Upgrade using the puppet_agent module

The [puppetlabs-puppet_agent module](./install_upgrading_agents.html#upgrade-agents-using-the-puppetagent-module) allows you to upgrade several agents at once. This module handles all the latest version to version upgrades.

If you have Windows agents running a 3.8 version, use this module to upgrade.

### Upgrade with PE package management

If you used PE package management to install your Windows agents, you can use that same method to upgrade them.

>**Note:** In the following example, the `<PUPPET MASTER FQDN>` portion of the installer script refers to the fully qualified domain name of the Puppet master. Additionally, this FQDN must be fully resolvable by the Windows machine on which you're installing the agent.

1. To upgrade a Windows agent using PE package management, run the following command:

   ~~~
   [Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; $webClient = New-Object System.Net.WebClient; $webClient.DownloadFile('https://<PUPPET MASTER FQDN>:8140/packages/latest/install.ps1', 'install.ps1'); .\install.ps1
   ~~~

### Upgrade with the .msi package

You can use the Windows agent .msi package to upgrade.

1. [Download](http://info.puppetlabs.com/download-pe.html) the appropriate new version of the .msi package.
2. Follow the installer dialog.

The installer will handle stopping and re-starting the Puppet agent service.

When upgrading, the installer does not replace any settings in the main puppet.conf configuration file, but it can add previously unspecified settings if they are provided on the command line.

The following version upgrades are supported:

* 32-bit to PE 2015.2 32-bit. Program data is the same, for example c:\programData\Puppet Labs
* 32-bit PE to 64-bit PE. You can only do this upgrade from PE 3.8 to a version post-2015.2. This upgrade changes the default  from `c:\Program Files(86)\` to `c:\Program Files\`.

If you have installed into a custom location, that location will not be rerouted.

## Switching to an older version of PE

If you’re downgrading from the current version of PE to an older version, we recommend that you uninstall PE from your agents first, and then install the version of your choice. This guarantees the cleanest install.

You can also use the Windows .msi to switch versions. It will add PE to the system path, with the following caveats:

* Processes that were already running will not see the change until they are restarted.
* Puppet will expand your variables. This is a problem if the path looks like this: `PATH = %SystemRoot\System32`. Puppet will change this to `PATH = c:\Windows\System32`. This is a known issue.
* In some cases, files from the newer version will be left on your machine.

