---
layout: default
title: "Upgrading PE agents: *nix"
canonical: "/pe/latest/install_upgrading_agents.html"
---

You can upgrade your *nix PE agents as new versions become available.

There are two paths to choose from when upgrading your agents:

* Use the puppetlabs-puppet\_agent module, which handles all the latest version to version upgrades. This choice involves downloading and installing a module from the Puppet Forge.

  >**Note:** If you have 3.8 agents to upgrade to the latest versions of PE, use this module. 

* Use PE package management to upgrade individual agents, as described in [Upgrade agents using PE package management](#upgrade-agents-using-pe-package-management). 

Before you begin upgrading your agents, upgrade your [monolithic](./upgrade_mono.html) or [split](./upgrade_split.html) Puppet master.

## Upgrade agents using the puppet_agent module

The [puppetlabs-puppet_agent module](https://forge.puppetlabs.com/puppetlabs/puppet_agent) allows you to upgrade several agents at once. This module handles all the latest version to version upgrades.

When upgrading your agents, we recommend that you first upgrade a subset of your agents, and once you've verified the upgrade, upgrade your remaining agents. The following procedure assumes you will do this.

The general procedure is as follows:

- Classify your Puppet master with the appropriate repos that correspond to the platforms and architectures of your agents.
- Create a classification node group and a rule to pull in the subset of nodes you want to upgrade. In this task, we create an example rule to upgrade all agents running a RedHat OS.

>**Note:** The puppetlabs-puppet_agent module can be used for *nix and Windows agents.

1. **On your Puppet master**, download and install the puppetlabs-puppet\_agent module. Run `puppet module install puppetlabs-puppet_agent`.

2. In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Masters** group.

3. On the **Classes** tab, in the **Add a new class** field, begin typing `pe_repo`, and select the repo class from the list of classes.

   The repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`.

   >**Important:** If you need to add any repo classes, you need to [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the Puppet master before those changes will be available in the classifier.

4. In the console, click **Classification** > **Add group**, and specify options for the new node group:

   Option | Value
   -------|------
   Parent name | Select the name of the classification node group that you want to set as the parent to this group, in this case, **All Nodes**.
   Group name | Enter a name that describes the role of this classification node group, for example, *agent_upgrade*.
   Environment | Select the environment your agents are in.
   Environment group | *Do not* select this option.

5. Click **Add**.

6. Click **Add membership rules, classes, and variables**.

7. On the **Rules** tab, create a rule to add agents to this group:

   Option | Value
   -------|------
   Fact | Select **osfamily**
   Operator | Select **=**
   Value | Enter **RedHat**

8. Click **Add rule**, and commit changes.

   Your RedHat agents are now pinned to the upgrade group.

9. Still in the group you've created, click the **Classes** tab, and in the **Add new class** field, add the `puppet_agent` class.

   If you don't immediately see the class, click **Refresh** to update the classifier.

   >**Note**: If you've changed the prefix parameter of the `pe_repo` class in your PE Master node group, set the `puppet-agent source` parameter of the upgrade group to `https://<MASTER HOSTNAME>:8140/<Prefix>`.

10. Click **Add class**, and commit changes.

11. On the Puppet agents you are upgrading, run Puppet with `/opt/puppet/bin/puppet agent -t`.

After Puppet runs, you can verify the upgrade by running `/opt/puppetlabs/bin/puppet --version`.

## Upgrade agents using PE package management

The Puppet master hosts a package repo used to install agents in your PE infrastructure, and you can use this same repo to upgrade your *nix agents.

Before upgrading your agents, check the [agent components table](./install_what_and_where.html#agent-components-on-all-nodes) to ensure your Puppet agent version is correct for the PE version you're upgrading to.

If you are using PE package management, the `pe_repo`, hosted on the Puppet master, will get upgraded when you upgrade the Puppet master.

There are two upgrade paths to choose from, depending whether or not you [installed your agents using the PE package installation script](./install_agents.html#installing-puppet-agents-with-the-package-installation-script):

- Upgrade agents installed with the installation script
- Upgrade agents installed without the installation script

>**Note:** If you encounter SSL errors during the upgrade process, ensure your agent's OpenSSL is up to date and matches the Puppet master's version. You can check the Puppet master's OpenSSL versions with `/opt/puppetlabs/puppet/bin/openssl version` and the agent's version with `openssl version`.

### Upgrade agents installed with the installation script

If you installed your agents with the installation script, you can use the same script to upgrade them.

1. [Upgrade your Puppet master](./upgrade_mono.html).
2. SSH into the agent node you want to upgrade.
3. Depending on the type of *nix agent you have, run one of the following commands:

   - Most *nix:
   
     ~~~
     /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
     ~~~

   - OS X, Solaris, or AIX agents:

     ~~~
     curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
     ~~~

### Upgrade agents installed without the installation script

If you did not install your agents with the installation script, you can still use it to upgrade, but you must take a few additional steps.

1. [Upgrade your Puppet master](./upgrade_mono.html).
2. In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Masters** group.

2. On the **Classes** tab, in the **Class name** field, enter `pe_repo` and select the repo class from the list of classes.

   >**Note**: the repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`

3. Click **Add class**, and commit changes.

4. Run `puppet agent -t` to configure the Puppet master node using the newly assigned class.

   The new repo is created in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/<PLATFORM>/`.

5. Depending on the type of *nix agent you have, run one of the following commands:

   - Most *nix agents:
   
     ~~~
     /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
     ~~~

   - OS X, Solaris, or AIX agents:

     ~~~
    curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
     ~~~

PE services should restart automatically after the upgrade. But if you want to check that everything is working correctly, you can run `puppet agent -t` on your agents to ensure that everything is behaving as it was before upgrading. Generally speaking, it's a good idea to run Puppet right away after an upgrade to make sure everything is hooked and has the latest configuration.

## Upgrading agents in a Puppet Enterprise infrastructure without internet access

[apt]: https://forge.puppetlabs.com/puppetlabs/apt
[inifile]: https://forge.puppetlabs.com/puppetlabs/inifile
[stdlib]: https://forge.puppetlabs.com/puppetlabs/stdlib
[puppet_agent]: https://forge.puppetlabs.com/puppetlabs/puppet_agent

In some cases, your PE infrastructure does not have access to the outside internet, but you can still upgrade your agents.

1. Follow **Option 1** or **Option 3** in [installing agents without internet access](./install_agents.html#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access) to get the agent packages into your infrastructure.
2. Download the [puppetlabs-apt][apt], [puppetlabs-inifile][inifile], [puppetlabs-stdlib][stdlib] and [puppetlabs-puppet_agent][puppet_agent] modules from the Puppet Forge, and transfer them your Puppet master.
3. Follow the Puppet agent instructions provided above.












