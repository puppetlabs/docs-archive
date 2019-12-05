---
layout: default
title: "Agent install quick start guide"
canonical: "/pe/latest/quick_start_install_agents_windows.html"
---


### Overview

>**Important**: This information describes how to install Puppet agents on Windows nodes. The Puppet master component, described in the "PE Install Quick Start Guide," must be run on a supported Linux machine.

The following instructions for installing Puppet agents on nodes running [supported versions](./install_system_requirements.html#puppet-agent-platforms) of the Windows operating system. Refer to the [*nix agent installation quick start guide](./quick_start_install_agents_nix.html) for instructions on installing Puppet agents on nodes running *nix operating systems.

For more information about Puppet agents, as well as the components that make up your PE deployment, visit the [installation overview](./install_basic.html) in the PE docs.

For more information about Windows Puppet agents, including additional installation choices, refer to [Installing Windows Agents](./install_windows.html).

>**Prerequisites**: This guide assumes you've [installed a monolithic PE deployment](./quick_start_install_mono.html) and have the Puppet master, the PE console, and PuppetDB up and running on one node.

### Installing Windows Puppet Enterprise agents

[downloadpe]: http://info.puppetlabs.com/download-pe.html
[startmenu]: ./images/windows/start_menu.png
[server]: ./images/windows/wizard_server.png
[node_request]: ./images/console/request_indicator.png

The PE Windows installer is a standard Windows .msi package and will run as a graphical wizard.

The installer must be run with elevated privileges. Installing Puppet **does not** require a system reboot.

1. [Download][downloadpe] and run the installer.

2. When prompted by the install dialog, provide the hostname of your Puppet master server.

   ![Puppet master hostname selection][server]

3. Load a list of currently pending node requests by clicking **Nodes** > **Unsigned certificates**.

4. Click __Accept All__ to approve the requests and add the node.

> The Puppet agent can now retrieve configurations from the master the next time Puppet runs.

### After installation

* Puppet agent will be running as a Windows service, and will fetch and apply configurations every 30 minutes (by default). You can now assign classes to the node as normal; see [Getting started with classification](./console_classes_groups_getting_started.html) for more details. After the first Puppet run, the MCollective service will also be running and the node can now be controlled with MCollective. The Puppet agent service and the MCollective service can be started and stopped independently using either the service control manager UI or the command line `sc.exe` utility; see [Running Puppet on Windows]({{puppet}}/services_commands_windows.html) for more details.

* The Start Menu will contain a Puppet folder, with shortcuts for running Puppet agent manually, running Facter, and opening a command prompt for use with the Puppet tools. See [Running Puppet on Windows][running] for more details.

    ![Start Menu icons][startmenu]

* Puppet is automatically added to the machine's PATH environment variable. This means you can open any command line and call `puppet`, `facter` and the few other batch files that are in the `bin` directory of the [Puppet installation](./install_windows.html#program-directory). This will also add necessary items for the Puppet environment to the shell, but only for the duration of execution of each of the particular commands.

----------------------
Next: [Module install](./quick_start_module_install_windows.html)
