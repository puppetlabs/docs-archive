---
layout: default
title: "Puppet Plugin for VMware vRealize Orchestrator User Guide"
canonical: "/pe/latest/vro_intro.html"
description: "Instructions on installing, configuring, and using the Puppet plug-in for VMware vRealize Orchestrator (vRO)."
---

The VMware vRealize Orchestrator (vRO) Puppet plug-in v2.0 provides tools and out-of-the-box components that easily create, provision, and manage application stacks on virtual servers.

> **Note:** This user guide walks you through setting up a **reference implementation** of the plug-in, vRO, and vRA, and isn't designed for out-of-the-box use in production. Once you've completed this guide, you should have a working environment and examples with which you can develop your own Puppet code, vRO workflows, and vRA blueprints.

## What the Puppet vRO plug-in can do

With a single click, Puppet, vRO, and vRA can automatically create a VM, install the Puppet agent, [autosign](/puppet/latest/reference/ssl_autosign.html) its certificate, add Puppet [roles and profiles](/pe/latest/r_n_p_intro.html), install the required Puppet modules and the software they configure, and set up the server for immediate use.

If you're new to Puppet and vRO, you can use vRO and vRA to set up a live, functional Puppet-managed system with much less effort than building one manually.

If you're experienced with vRO, vRealize Automation (vRA), and Puppet, you can also use this plug-in to model common Puppet workflows as vRO workflows and vRA blueprints, then deploy them just as easily as other VMs while maintaining the advantages of a Puppet-managed infrastructure.

> **Note:** This version of the plug-in was built with new and more robust features, including compatibility with new features introduced in Puppet 4. This plug-in doesn't support in-place upgrades from the previous vRO Puppet plug-in version (1.0); if you're using the version 1.0 of the plug-in, you must remove it before installing version 2.0.

## Prerequisites

The vRO Puppet plug-in 2.0 requires:

-   A Puppet master server running Puppet Enterprise 2016.4 or newer, or open source Puppet 4.6.2 or newer
-   vRealize Orchestrator 6 or newer
-   vRealize Automation 6 or newer

Agent nodes being managed by Puppet must run an operating system [supported by the Puppet agent](/pe/latest/sys_req_os.html).

> **Note:** The 32-bit version of Microsoft Windows Puppet agent is **not** compatible with vRO plug-in management. You must use the 64-bit (x64) agent.

### Using a reference implementation

The following instructions guide you through installing and configuring a reference implementation of the vRO Puppet plug-in using Puppet Enterprise 2016.4.2 and vRO/vRA 7.1. This implementation is designed to create a development environment with vRO, vRA, and Puppet running as quickly as possible in order to help you learn how these tools work together.

The reference implementation isn't designed with production deployments in mind. Once you're familiar with how the plug-in works, you can install it into your production vRO/vRA infrastructure and build compatible workflows and blueprints.

The plug-in works with many implementations of Puppet, vRO, and vRA, including vRO/vRA 6 and open source Puppet 4.6.2 and newer. While you can use these instructions to set up this plugin with other versions of Puppet and vRO/vRA, we recommend using this reference implementation for development.

> **Note:** If you're already experienced with Puppet, vRO, vRA, and the vRO Puppet plug-in, see [part 3](#part-3--managing-and-provisioning-infrastructure-with-vra-and-puppet) for a quick reference of properties and usage.

## Part 1: Install and configure Puppet Enterprise

1.  Review the Puppet Enterprise [hardware](/pe/latest/sys_req_hw.html) and [operating system](/pe/latest/sys_req_os.html) requirements.

2.  [Install Puppet Enterprise](/pe/latest/install_basic.html) on a VM or server. This will serve as the Puppet master server, and must be accessible over the network from the vRO appliance or server.

    > **Note:** For this reference implementation of the vRO plug-in, this must be a new, clean installation of Puppet Enterprise with Code Manager disabled.

3.  Add the vRO Puppet plug-in [starter pack content](https://github.com/puppetlabs/puppet-vro-starter_content) by cloning its GitHub repository into an environment.

    On a clean installation, run the vRA node classification setup script with root privileges from within the `puppet-vro-starter_content` repository:

        cd puppet-vro-starter_content
        sudo bash scripts/vra_nc_setup.sh

    > **Note:** This script doesn't randomize the UUID for the classification group it creates, so it will create or replace the same group instead of creating a new group if it is run more than once.

    The starter content repository provides reference implementations of Puppet [roles and profiles](/pe/latest/r_n_p_intro.html) for Linux and Windows web server stacks, utility scripts to prepare the master server for vRO, and a templated autosigning script. Once you understand how Puppet, vRO, and vRA work together, you can use these reference implementations to help build your own Puppetized vRO/vRA implementations.

    If you're already experienced with Puppet, vRO, and vRA, you can replace this reference implementation with your own code or control repository.

4.  Ensure that the Puppet master has a valid DNS hostname and [NTP](/pe/latest/quick_start_ntp.html) configured. If you don't have or use a DNS server, provide a valid hostname for the server's IP address in the master server's hosts file (typically `/etc/hosts`).

    > **Note:** Make sure that a hostname is properly configured on the machines you’re installing PE on. _All nodes_ must know their own hostnames. This can be done by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly. Setting the hostname usually involves the `hostname` command and one or more configuration files, while the exact method varies by platform.
    >
    > Additionally, all nodes must be able to reach each other by name. This can be done with a local DNS server, or by editing the hosts file on each node (such as `/etc/hosts` on a Linux node) to point to the proper IP addresses.

5.  Initiate a Puppet run on the master server:

        sudo puppet agent -t

    The vRO starter content creates a user account on the Puppet master (`vro-plugin-user`, default password `puppetlabs`) and adds rules to the `sudoers` file allowing it to run commands with elevated privileges as required by the plug-in.

    It also adds the following settings to the master's `sshd_config`:

    ```
    PermitRootLogin yes
    PasswordAuthentication yes
    ChallengeResponseAuthentication no
    ```

> **Note:** If you do not allow a sudo-capable user to run commands for vRO --- for instance, if you remove the `puppet-vro-user` account or revoke its sudoers privileges --- you will need to provide vRO with remote access to a user account on the master with those capabilities, or to the master's root user, which is insecure.

## Part 2: Install and configure the Puppet vRO plug-in

> **Note:** If you haven't yet installed vRO, refer to the [vRO documentation](https://www.vmware.com/support/pubs/orchestrator_pubs.html).

### Install the vRO plug-in

1.  Download the Puppet plug-in's .vmoapp package from the [VMware Solution Exchange](https://marketplace.vmware.com/vsx/solutions/puppet-plugin-for-vrealize-automation).

2.  Login to the vRO server's control center at `https://<VRO-SERVER-IP-ADDRESS>:8283/vco-controlcenter`.

3.  Click the Plugins tab.

4.  Click the Install plug-in button.

    Read and accept the EULA, then click Install.

5.  Install the vRO Puppet plug-in's .vmoapp package downloaded from the VMware Solution Exchange.

6.  After the installation confirmation message appears, click the Startup Options link in the message reminding you to restart the Orchestrator server.

7.  On the Startup Options page, click the Restart button under the Current Status heading.

### Download and install the vRO client

1.  From the main vRO web interface page, click the Start Orchestrator Client link to download the Java vRO client.

2.  Open the client from the location where you downloaded it.

    You can confirm that the Puppet plug-in content is available by opening the Library/Puppet folder in the workflows tab of the client's left pane.

### Add the Puppet master to vRO

vRO must be made aware of a Puppet master server by adding it to the inventory.

1.  In the vRO client, open the workflow tab.

2.  Navigate to Library/Puppet/Configuration

3.  Click the Add a Puppet Master workflow.

4.  Click the Run button in the right pane to start the workflow.

    A window appears with common parameters required for vRO to access the master, such as the master's display name, hostname or IP address, SSH port, and SSH/RBAC credentials.

5.  Fill out the fields, then click Save.

    > **Note:** If you do not provide a root user's credentials, choose Yes for the "Use sudo for shell commands on this master" option. The vRO plug-in must be able to run elevated shell commands on the master to perform most tasks.

    After clicking Save, the workflow begins to run.

Once the workflow has finished, you can view the master server in the inventory tab of the vRO client's left pane, under Puppet.

You can confirm that the process successfully finished by selecting the master server and confirming that the detected Puppet product and version facts appear.

> **Tip:** To troubleshoot unexpected results, open the workflow's results in the workflow tab, and then consult the Logs tab in the right pane.

### Configure vRO workflows

Before you can run workflows in vRO, you need to set an active Puppet master.

1.  In the vRO client's workflows tab, open the EventBroker Install PE agent workflow.

2.  In the right pane, click the Pencil icon to edit the workflow.

3.  In the workflow's General tab, edit the value of the activePuppetMaster attribute.

    A window appears containing valid items from your inventory.

4.  Select the Puppet master you [previously added](#add-the-puppet-master-to-vro) from the inventory, then click Select.

5.  Save and close the workflow.

Repeat the above process for the EventBroker Purge PE Agent Node workflow.

## Part 3: Managing and provisioning infrastructure with vRA and Puppet

> **Note:** If you haven't yet installed vRA, refer to the [vRA documentation](http://pubs.vmware.com/vra-70/index.jsp).

Once vRO and the Puppet plug-in are configured, you can use vRealize Automation (vRA) to request servers using blueprints.

### Requesting VMs using blueprints

Out of the box, the vRO Puppet plug-in provides reference blueprints that deploy a basic web server stack in a vSphere VM:

-   PE Linux Webserver, which deploys a CentOS 7 server running Apache.
-   PE Windows Webserver, which deploys Windows Server 2012R2 running IIS.

By requesting a VM with one of these blueprints, Puppet, vRO, and vRA together automatically:

-   Create a new VM with pre-defined specifications.
-   Install the Puppet agent on the VM.
-   Autosign the VM's certificate with Puppet.
-   Add Puppet role and profile classes to the VM toward a specific application stack.
-   Install and configure the required Puppet modules, as well as the software those modules will configure.
-   Configure the server to publish a simple website and populate a basic home page from a template.

To build a VM via one of these blueprints:

1.  From the Catalog tab in the vRA web interface, request a PE Linux Webserver or PE Windows Webserver.

2.  Review the VM's general traits by clicking on the VM (for instance, CentOS_7_vSphere_VM for the PE Linux Webserver). Note that in addition to the CPU, memory, and storage settings, the Puppet plug-in adds puppetCodeEnvironment and puppetRoleClass traits with values provided by the blueprint.

3.  Click Submit.

    vRA will begin provisioning the server. You can track its progress in the Infrastructure tab, where the Status column will update with each step of the provisioning process.

    You can also view logs of the run in the Logs tab of the workflow's result in the vRO client.

### Designing blueprints with Puppet features

> **Note:** For detailed information about designing vRA blueprints, consult the [vRA documentation](http://pubs.vmware.com/vra-70/index.jsp).

The provided blueprints and workflows serve as reference implementations of the vRO Puppet plug-in's capabilities. The [starter pack content](#part-1--install-and-configure-puppet-enterprise) contains sample Linux and Windows web server blueprints, as well as the Puppet modules, [roles, and profiles](/pe/latest/r_n_p_intro.html) that power those workflows. Consult or modify these blueprints and starter content when designing your own vRO workflows and vRA blueprints.

You can also consult the [starter pack's blueprints README](https://github.com/puppetlabs/puppet-vro-starter_content/blob/00ec0045a28ad2960353cb73e4df13ec086bfe0b/blueprints/README.md) at `puppet-vro-starter_content/blueprints/README.md` for help when importing blueprints using [VMware CloudClient](https://developercenter.vmware.com/tool/cloudclient/4.1.00).

Puppet plug-in features are available to blueprint designers as Properties (such as "Linux Puppet VM") in the vRA blueprint designer.

After adding a Puppet plug-in property to a blueprint, new merged properties will be available. For instance, you can set the autosigning shared secret, SSH password, and SSH username as properties of a Linux Puppet VM.

The vRO Puppet plug-in's workflows are also available to the workflow subscription designer, allowing you to associate events and conditions with workflows as part of a blueprint.

#### vRO/vRA property reference

The vRO Puppet plug-in uses the following properties for blueprint and workflow development:

| vRO JavaScript variable name | vRA property name            | Type          | Description                                                                                                   |
|------------------------------|------------------------------|---------------|---------------------------------------------------------------------------------------------------------------|
| puppetRoleClass              | Puppet.RoleClass             | string        | The fully qualified Puppet class that implements the node's role.                                             |
| puppetCodeEnvironment        | Puppet.CodeEnvironment       | string        | The environment on the Puppet master in which vRO should look for Puppet code.                                |
| puppetNodeCertname           | Puppet.Node.Certname         | string        | The Puppet agent sets this based on the node's `certname`, which is based on its fully qualified domain name. |
| puppetAutosignSharedSecret   | Puppet.Autosign.SharedSecret | secureString  | The shared secret that nodes should provide to the Puppet master in order to autosign certificate requests.   |
| sshUsername                  | Puppet.SSH.Username          | string        | Username used to connect to a node via SSH.                                                                   |
| sshPassword                  | Puppet.SSH.Password          | secureString  | Password used to connect to a node via SSH.                                                                   |
| winRMUsername                | Puppet.WinRM.Username        | string        | Username used to connect to a node via WinRM.                                                                 |
| winRMPassword                | Puppet.WinRM.Password        | secureString  | Password used to connect to a node via WinRM.                                                                 |

#### Encrypting content with eyaml

Securing passwords used in the manifest is beyond the scope of this reference implementation. As a starting point, many Puppet deployments use [Hiera](/hiera/), a key/value lookup tool for configuration, with [eyaml, or encrypted YAML](https://puppet.com/blog/encrypt-your-data-using-hiera-eyaml), to solve this problem. This solution not only provides secure storage for the password value, but also provides parameterization to support reuse, opening the door to easy password rotation policies across an entire network of nodes.

## Troubleshooting

### Purging a node doesn't update CRLs on the Puppet master

Workflows that purge nodes, such as the EventBroker Purge PE Agent Node workflow, use the `puppet node purge` command. After being purged, those nodes are not added to the Puppet master's Certificate Revocation List (CRL). This means these nodes might still be able to connect to the master after being purged.

After purging a node, you must run the following on the master to update the CRL:

```
puppet cert clean -y <NODE-HOSTNAME>
pgrep -f puppet-server | xargs kill -HUP
```

## Release notes

For known issues and details about specific plug-in releases, see the [release notes](./vro_release_notes.html).
