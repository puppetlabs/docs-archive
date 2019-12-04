---
layout: default
title: "Install *nix agents quick start guide"
canonical: "/pe/latest/quick_start_install_agents_nix.html"
---

A node running the Puppet agent application (in other words, a node managed by Puppet) is usually referred to as an **agent**. This quick start guide installs Puppet agents on nodes running *nix operating systems. 

Before you begin, make sure you've [installed a monolithic PE deployment](./quick_start_install_mono.html) and have the Puppet master, the PE console, and PuppetDB up and running on one node.

>#### FAQs
>
> * [How do I install a Puppet agent on a Windows machine?](./quick_start_install_agents_windows.html)
> * [How do I install Puppet agents if I'm not connected to the internet?](./install_agents.html#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access)

<!--Concept-->
## How does a Puppet agent work?

Periodically, a Puppet agent will send facts to a Puppet master and request a catalog. The master compiles the catalog using several sources of information, and returns the catalog to the agent.

Once it receives a catalog, the Puppet agent applies it by checking each resource the catalog describes. If it finds any resources that are not in their desired state, the agent will make any changes necessary to correct them. (Or, in no-op mode, it will report on what changes would have been needed.)

After applying the catalog, the Puppet agent submits a report to its master. Reports from all the agents are stored in PuppetDB and can be accessed in the PE console.

<!--Task-->
## Step 1a: Install an agent with the same OS and architecture as the Puppet master

_Follow these steps if your agent node and your Puppet master have the same OS and architecture. Otherwise, go to [Step 1b](./quick_start_install_agents_nix.html#step-1b-install-an-agent-with-a-different-os-and-architecture-than-the-puppet-master)._

1. Log into your agent node and run:

   ```
   curl -k https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
   ```

   This script detects the OS on which the agent is running, sets up an apt, yum, or zipper repo that refers back to the Puppet master, and then pulls down and installs the `puppet-agent` packages.

2. After installation is complete, [approve the certificate request](./quick_start_install_agents_nix.html#step-2-approve-the-certificate-request) to approve the new agent's certificate request in the console.

<!--Task-->
## Step 1b: Install an agent with a different OS and architecture than the Puppet master

_Follow these steps if your agent node and your Puppet master do not have the same OS and architecture. Otherwise, go to [Step 1a](./quick_start_install_agents_nix.html#step-1a-install-an-agent-with-the-same-os-and-architecture-as-the-puppet-master)._

[add_repo]: ./images/quick/add_repo.png
[node_request]: ./images/console/request_indicator.png

This example describes adding an agent running Debian 6 on AMD64 hardware.
As you complete the steps, modify the commands to match your agent's OS and architecture. 

1. In the console, click **Classification**, and in the **PE Infrastructure** node group, select the **PE Master** group.

2. On the **Classes** tab, in the __Add new class__ field, enter `pe_repo` and select the class `pe_repo::platform::debian_6_amd64` -- or whichever class matches your agent -- from the list of classes.

   The repo classes are listed as `pe_repo::platform::<agent_os_version_architecture>`

3. Click __Add class__, and commit changes.

   The class you selected now appears in the list of classes for the __PE Master__ group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

4. From the command line on the Puppet master, run `puppet agent -t`.

   This configures the Puppet master node using the newly assigned class.

   The new repo is created in `/opt/puppetlabs/server/data/packages/public`.

5. SSH into your agent node, and run:

   ```
   curl -k https://<master.example.com>:8140/packages/current/install.bash | sudo bash
   ```

   If you wish to install a version of PE other than the most recent release, replace `current` in the script with a specific [PE version number](./overview_version_table.html), in the form of `2016.x.x`.

   Depending on your platform, the method for downloading the script may vary.

6. The installer installs and configures the Puppet Enterprise agent.

7. After installation is complete, [approve the certificate request](./quick_start_install_agents_nix.html#step-2-approve-the-certificate-request) to approve the new agent's certificate request in the console.

>#### FAQs
>
> * [What do I do if my Puppet master uses a proxy server to access the internet?](./release_notes_known_issues_install.html#install-agents-with-different-os-when-puppet-master-is-behind-a-proxy)

<!--Task-->
## Step 2: Approve the certificate request

During installation, the agent node contacts the Puppet master and requests a certificate. To add the node to the console and to start managing its configuration, you must approve its certificate request. 

1. In the console, load a list of currently pending node requests by clicking **Unsigned Certs**.

2. Click the __Accept All__ button to approve the request and add the node.

The Puppet agent can now retrieve configurations from the master the next time Puppet runs.

<!--Task-->
## Step 3: Test the Puppet agent node

By default, the agent fetches configurations from the Puppet master every 30 minutes. (You can [configure this interval in the puppet.conf file]({{puppet}}/config_file_main.html) with the `runinterval` setting.) However, you can manually trigger a Puppet run from the command line at any time.

1. **On the agent,** log in as root and run `puppet agent --test`. This triggers a single Puppet run on the agent with verbose logging.

   If you receive a `-bash: puppet: command not found` error, then the directory that PE installs its binaries in, `/opt/puppetlabs/bin`, isn't included in your default `$PATH`. To include these binaries in your default `$PATH`, add them by running `PATH=/opt/puppetlabs/bin:$PATH;export PATH`.

2. Note the long string of log messages, ending with `Notice: Applied catalog in [...] seconds`.

> You are now fully managing the agent node! It has checked in with the Puppet master for the first time and received its configuration info. It will continue to check in and fetch new configurations every 30 minutes. 
>
> Next, you'll begin learning how to configure your agents with Puppet code, beginning with pre-built chunks of Puppet code called **modules**. [Click here when you're ready to download and install your first module.](./quick_start_module_install_nix.html)  




