---
layout: default
title: "Upgrading Puppet agents"
canonical: "/pe/latest/install_upgrading_agents.html"
---


There are two paths to choose from when upgrading your Puppet agents:

* The puppetlabs-puppet\_agent module allows you to upgrade several agents at once, as described in [Upgrade agents using the puppet_agent module](#upgrade-agents-using-the-puppetagent-module). This module supports upgrades of agents running 3.8 or later.

* You can use the `pe_repo` to upgrade individual agents, as described in [Upgrade agents using `pe_repo`](#upgrade-agents-using-perepo). If you're upgrading 3.8 agents, use the puppet_agent module.

## Upgrade agents using the puppet_agent module

To upgrade your Puppet agents, download and install the [puppetlabs-puppet_agent module](https://forge.puppetlabs.com/puppetlabs/puppet_agent). We created this module to handle some of the heavy-lifting in the move to the "AIO" Puppet agent. This module automates important path and configuration changes to make the upgrade process much easier.

> **Tip**: When upgrading your agents to PE 2015.2, we recommend that you first upgrade a subset of your agents, and once you've verified the install, upgrade your remaining agents. The following procedure assumes you will do this.

**To upgrade your Puppet agents**:

1. Upgrade your [monolithic](./upgrade_mono.html) or [split](./upgrade_split.html) Puppet master.
2. Ensure your Puppet master is classified with the appropriate repos that correspond to the platforms and architectures and architectures of your agents.

   a. Navigate to the **PE Masters** group.

   b. Click the **Classes** tab.

   c. In the **Class name** field, begin typing `pe_repo`, and select the repo class from the list of classes.

   The repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`.

   >**Note**: If you need to add any repo classes, you need to run Puppet on the Puppet master before those changes will be available in the classifier.

3. **On your Puppet master**, download and install the puppetlabs-puppet\_agent module. Run `puppet module install puppetlabs-puppet_agent`.

4. In the PE console, create a classification node group and a rule to pull in the subset of nodes you want to upgrade. In this example, we create a rule to upgrade all agents running the RedHat OS.

   1. In the PE console, click **Nodes** > **Classification**, then click **Add group**.

   2. Specify options for the new node group:

      - **Parent name** -- Select the name of the classification node group that you want to set as the parent to this group, in this case, **All Nodes**.

      - **Group name** -- Enter a name that describes the role of this classification node group, for example, *agent_upgrade*.

      - **Environment** -- Select the environment your agents are in.

      - **Environment group** -- *Do not* select this option.

   3. Click **Add**.

   4. Click the link to **add membership rules, classes and variables**.

   5. On the **Rules** tab, create a rule to add agents to this group:

      - **Fact** -- Select **osfamily**.

      - **Operator** -- Select **=**.

      - **Value** -- Enter **RedHat**.

   6. Click **Add rule**, then commit changes.

   Your RedHat agents are now pinned to the upgrade group.

5. Still in the group you've created, click the **Classes** tab, and in the **Add new class** field, add the `puppet_agent` class.

   If you don't immediately see the class, click **Refresh** to update the classifier.

   > **Note**: If you've changed the prefix parameter of the `pe_repo` class in your PE Master node group, set the `puppet-agent source` parameter of the upgrade group to `https://<MASTER HOSTNAME>:8140/<Prefix>`.

6. Click **Add class**, and then click the **Commit changes** button.

7. On the Puppet agents you are upgrading, run Puppet with `/opt/puppet/bin/puppet agent -t`.

After Puppet runs, you can verify the upgrade by running `/opt/puppetlabs/bin/puppet --version`.

## Upgrade agents using pe_repo

Before upgrading your agents, check the [Agent Components table](./install_what_and_where.html#agent-components-on-all-nodes) to ensure your AIO Puppet agent version is correct for the PE version you're upgrading to.

If you are using the PE package repo (`pe_repo`) hosted on the Puppet master, it will get upgraded when you upgrade the Puppet master.

**To upgrade your Puppet agents**:

1. Upgrade your Puppet master.
2. Follow the instructions for [using the agent install script](./install_agents.html#installing-puppet-agents-with-the-package-installation-script) to upgrade your agents.

   a. If the OS/architecture of your Puppet master is the same as your agents, choose [scenario 1](./install_agents.html#scenario-1-the-osarchitecture-of-the-puppet-master-and-the-agent-node-are-the-same).

   b. If the OS/architecture of your Puppet master is different than your agents, choose [scenario 2](./install_agents.html#scenario-2-the-osarchitecture-of-the-puppet-master-and-the-agent-node-are-different).

If you are using your own package manager (e.g., Satellite), the simplest way to upgrade agents is to upgrade the `puppet_agent` package in the repo your package manager (e.g., Satellite) is using.

PE services should restart automatically after the upgrade. But if you want to check that everything is working correctly, you can run `puppet agent -t` on your agents to ensure that everything is behaving as it was before upgrading. Generally speaking, it's a good idea to run Puppet right away after an upgrade to make sure everything is hooked and has the latest configuration.

## Upgrading agents in a Puppet Enterprise infrastructure without internet access

[apt]: https://forge.puppetlabs.com/puppetlabs/apt
[inifile]: https://forge.puppetlabs.com/puppetlabs/inifile
[stdlib]: https://forge.puppetlabs.com/puppetlabs/stdlib
[puppet_agent]: https://forge.puppetlabs.com/puppetlabs/puppet_agent

If your PE infrastructure does not have access to the outside internet, to upgrade your agents you should:

1. Follow **Option 1** or **Option 3** in [installing agents without internet access](./install_agents.html#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access) to get the agent packages into your infrastructure.
2. Download the [puppetlabs-apt][apt], [puppetlabs-inifile][inifile], [puppetlabs-stdlib][stdlib] and [puppetlabs-puppet_agent][puppet_agent] modules from the Puppet Forge, and transfer them your Puppet master.
3. Follow the Puppet agent instructions provided above.












