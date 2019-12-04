# Getting started: Installing Windows agents

After installing a PE master, you're ready to install Puppet agents on Windows nodes. The Puppet agent regularly pulls configuration catalogs from a Puppet master and applies them to the local system. In this way, agents maintain the configuration you want. This guide walks you through installing the agent and signing certificates, a process that is different from installing agents on \*nix nodes.

**Important:** The Puppet master component, described in the [PE Install getting started guide](install_puppet_enterprise_for_windows_getting_started_guide.md#), must be run on a supported Linux machine.

For more information about Puppet agents, as well as the components that make up your PE deployment, visit the [installation overview](installing.md)in the PE docs.

For more information about Windows Puppet agents, including additional installation choices, refer to [Installing Windows Agents.](installing_agents.md#)

## Install Windows Puppet Enterprise agents

The PE Windows installer is a standard Windows .msi package and will run as a graphical wizard.

### Before you begin

Install a [monolithic PE deployment](installing_pe.md#) so that the Puppet master, the PE console, and PuppetDB all run on one node. In addition, check that you're working with a [supported version of Windows](supported_operating_systems.md#).

### About this task

Use the MSI installer for a more automated installation process. The installer can configure `puppet.conf`, create CSR attributes, and configure the agent to talk to your master.

The installer must be run with elevated privileges. Installing Puppet does not require a system reboot.

### Procedure

1.  [Download](https://puppet.com/download-puppet-enterprise) and run the installer.

2.  When prompted, provide the hostname of your master, for example Puppet.

3.  Load a list of currently pending node requests by clicking **Unsigned Certs**.

4.  Click **Accept All** to approve the requests and add the node. The Puppet agent can now retrieve configurations from the master the next time Puppet runs.


### Results

For additional information on installing Windows agents with the .msi package, see [Install Windows agents using `msiexec` from the command line](installing_agents.md#) and [MSI properties.](installing_agents.md#)

## After installation

After you install a Puppet agent, you can automatically configure and manage your node.

-   Puppet agent runs as a Windows service, and fetches and applies configurations every 30 minutes \(by default\). You can now assign classes to the node. After the first Puppet run, the MCollective service will also run and the node can now be controlled with MCollective. The Puppet agent service and the MCollective service can be started and stopped independently using either the service control manager UI or the command line `sc.exe` utility.

-   The Start Menu contains a Puppet folder, with shortcuts for running Puppet agent manually, running Facter, and opening a command prompt for use with the Puppet tools.

-   Puppet is automatically added to the machine's PATH environment variable. This means you can open any command line and call `puppet`, `facter` and the few other batch files that are in the `bin` directory of the Puppet installation. This will also add necessary items for the Puppet environment to the shell, but only for the duration of execution of each of the particular commands.


