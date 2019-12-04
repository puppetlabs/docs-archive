---
layout: default
title: "Agent install quick start guide"
canonical: "/pe/latest/quick_start_install_agents_windows.html"
---

After installing a PE master, you're ready to install Puppet agents on Windows nodes. This guide walks you through the steps, which are different from the steps to install agents on *nix nodes. 

>**Important**: The Puppet master component, described in the "PE Install Quick Start Guide," must be run on a supported Linux machine.

For more information about Puppet agents, as well as the components that make up your PE deployment, visit the [installation overview](./install_basic.html) in the PE docs.

For more information about Windows Puppet agents, including additional installation choices, refer to [Installing Windows Agents](./install_windows.html).

<!--Task-->
## Install Windows Puppet Enterprise agents

[downloadpe]: http://info.puppetlabs.com/download-pe.html
[startmenu]: ./images/windows/start_menu.png
[server]: ./images/windows/wizard_server.png
[node_request]: ./images/console/request_indicator.png

The PE Windows installer is a standard Windows .msi package and will run as a graphical wizard.

The installer must be run with elevated privileges. Installing Puppet **does not** require a system reboot.

**Before you begin:** Install a monolithic PE deployment so that the Puppet master, the PE console, and PuppetDB all run on one node. In addition, check that you're working with a supported version of Windows. 

1. [Download][downloadpe] and run the installer.

2. When prompted by the install dialog, provide the hostname of your Puppet master server.

   ![Puppet master hostname selection][server]

3. Load a list of currently pending node requests by clicking **Unsigned Certs**.

4. Click __Accept All__ to approve the requests and add the node.
The Puppet agent can now retrieve configurations from the master the next time Puppet runs.

Related links: [Install Puppet Enterprise quick start guide](./quick_start_install_mono.html) and [Puppet agent platforms](./sys_req_os.html#puppet-agent-platforms)

<!--Concept-->
## After installation

After you install a Puppet agent, you can automatically configure and manage your node. 

* Puppet agent runs as a Windows service, and fetches and applies configurations every 30 minutes (by default). You can now assign classes to the node; see [Getting started with classification](./console_classes_groups_getting_started.html) for more details. After the first Puppet run, the MCollective service will also run and the node can now be controlled with MCollective. The Puppet agent service and the MCollective service can be started and stopped independently using either the service control manager UI or the command line `sc.exe` utility; see [Running Puppet on Windows]({{puppet}}/services_commands_windows.html) for more details.

* The Start Menu contains a Puppet folder, with shortcuts for running Puppet agent manually, running Facter, and opening a command prompt for use with the Puppet tools. See [Running Puppet on Windows][running] for more details.

    ![Start Menu icons][startmenu]

* Puppet is automatically added to the machine's PATH environment variable. This means you can open any command line and call `puppet`, `facter` and the few other batch files that are in the `bin` directory of the [Puppet installation](./install_windows.html#program-directory). This will also add necessary items for the Puppet environment to the shell, but only for the duration of execution of each of the particular commands.

----------------------
Next: [Module install](./quick_start_module_install_windows.html)
