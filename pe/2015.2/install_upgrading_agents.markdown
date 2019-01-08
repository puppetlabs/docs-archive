---
layout: default
title: "PE 2015.2» Installing » Upgrading"
subtitle: "Upgrading Puppet Agents"
canonical: "/pe/latest/install_upgrading_agents.html"
---


There are two paths to choose from when upgrading Puppet agents to PE 2015.2.3:

1. [Upgrade agents to PE 2015.2.3 from 3.8.x](#upgrade-agents-to-pe-201523-from-38x)
2. [Upgrade agents to PE 2015.2.3 from 2015.2.x](#upgrade-agents-to-pe201523-from-20152x)

Before beginning, make sure you've [upgraded your Puppet master and other PE components](./install_upgrading.html). Upgrades to PE 2015.2.1 are only supported from PE 3.8.2 or 2015.2.

## Upgrade Agents to PE 2015.2.3 from 3.8.x

If you want to upgrade your Puppet Agents to PE 2015.2.1, they must be running PE 3.8.2. Puppet masters running PE 2015.2 will manage agents running 3.8.x, but we recommend you upgrade to take advantage of the most recent features in the Puppet agent. For the PE 2015.2 series and beyond, we’ve gone to an all-in-one (AIO) Puppet agent package, [which contains several key Puppet components](./install_what_and_where.html#what).

To upgrade your Puppet agents, download and install the [puppetlabs-puppet_agent module](https://forge.puppetlabs.com/puppetlabs/puppet_agent). We created this module to handle some of the heavy-lifting in the move to the "AIO" Puppet agent. This module automates important path and configuration changes to make the upgrade process much easier.

> **Tip**: When upgrading your agents to PE 2015.2, we recommend that you first upgrade a subset of your agents, and once you've verified the install, upgrade your remaining agents. The following procedure assumes you will do this.

**To upgrade your Puppet agents**:

1. Upgrade your [monolithic](link) or [split](link) Puppet master.
2. Ensure your Puppet master is classified with the appropriate repos that correspond to the platforms and architectures and architectures of your agents.

   a. Navigate to the **PE Masters** group.

   b. Click the **Classes** tab.

   c. In the **Class name** field, begin typing `pe_repo`, and select the repo class from the list of classes.

   The repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`.

   >**Note**: If you need to add any repo classes, you need to run Puppet on the Puppet master before those changes will be available in the classifier.

3. **On your Puppet master**, download and install the puppetlabs-puppet\_agent module. Run `puppet module install puppetlabs-puppet_agent`.
4. In the PE console, create a classification node group and a rule to pull in the subset of nodes you want to upgrade. (In this example, we'll create a rule to upgrade all agents running the RedHat OS).

   a. Click **Nodes**.

   b. Click **Classification**.

   c. From the **Classification** page, in the **Node group name** field, enter the name your agent upgrade group (e.g., **agent_upgrade**).

   d. From the **Parent name** drop-down list, select **All Nodes**, and from the **Environment** drop-down list, choose the environment your agents are in.

   e. Click **Add group**.

   f. Select the group you've just created---you'll be taken to the **Rules** tab.

   g. Create a rule to add agents to this group. In the **Fact** field select **osfamily**. From the **Operator** drop-down list, select **is**, and in the **Value** field, enter **RedHat**.

   h. Click **Add rule**, and then click the **Commit changes** button.

   Your RedHat agents are now pinned to the upgrade group.

5. Still in the group you've created, click the **Classes** tab, and in the **Add new class** field, add the `puppet_agent` class.

   If you don't immediately see the class, click **Refresh** to update the classifier.

   > **Note**: If you've changed the prefix parameter of the `pe_repo` class in your PE Master node group, set the `puppet-agent source` parameter of the upgrade group to `https://<MASTER HOSTNAME>:8140/<Prefix>`.

6. Click **Add class**, and then click the **Commit changes** button.

7. On the Puppet agents you are upgrading, run Puppet with `/opt/puppet/bin/puppet agent -t`.

   >**Warning**: If you're upgrading EL 7 or SLES 12 agents, when performing this step, do not use the `mco runonce` command to run Puppet on all your agents, as this causes the upgrade to fail. A full explanation is available in [the known issues](./release_notes_known_issues_install.html#when-upgrading-el-7-or-sles-12-agents-do-not-use-the-mco-runonce-command) .

After Puppet runs, you can verify the upgrade by running `/opt/puppetlabs/bin/puppet --version`.

### Upgrade Agents to PE 2015.2.3 from 2015.2.x

If you are using the PE package repo hosted on the Puppet master, it will get upgraded when you upgrade the Puppet master.

You need to perform these steps:

1. Upgrade your Puppet master.
2. Follow the instructions for [using the agent install script](./install_agents.html#installing-agents-using-pe-package-management) to upgrade your agents.

   a. If the OS/architecture of your Puppet master is the same as your agents, choose [scenario 1](./install_agents.html#scenario-1-the-osarchitecture-of-the-puppet-master-and-the-agent-node-are-the-same).

   b. If the OS/architecture of your Puppet master is different than your agents, choose [scenario 2](./install_agents.html#scenario-2-the-osarchitecture-of-the-puppet-master-and-the-agent-node-are-different).

If you are using your own package manager (e.g., Satellite), the simplest way to upgrade agents is to upgrade the `puppet_agent` package in the repo your package manager (e.g., Satellite) is using.

For nodes running an OS that doesn't support remote package repos (e.g., Windows) you'll need to use the installer script on the PE tarball as you did for the Puppet master, etc. On each node with a Puppet agent, unpack the PE 2015.2.3 tarball and run the `puppet-enterprise-installer` script. The installer will detect the version from which you are upgrading and answer as many installer questions as possible based on your existing deployment. Note that the agents on your Puppet master, PE console, and PuppetDB nodes will have been updated already when you upgraded those nodes.

PE services should restart automatically after the upgrade. But if you want to check that everything is working correctly, you can run `puppet agent -t` on your agents to ensure that everything is behaving as it was before upgrading. Generally speaking, it's a good idea to run Puppet right away after an upgrade to make sure everything is hooked and has the latest configuration.

### Upgrading Agents in a Puppet Enterprise Infrastructure Without Internet Access

[apt]: https://forge.puppetlabs.com/puppetlabs/apt
[inifile]: https://forge.puppetlabs.com/puppetlabs/inifile
[stdlib]: https://forge.puppetlabs.com/puppetlabs/stdlib
[puppet_agent]: https://forge.puppetlabs.com/puppetlabs/puppet_agent

If your PE infrastructure does not have access to the outside internet, to upgrade your agents you should:

1. Follow **Option 1** or **Option 3** in [installing agents without internet access](./install_agents.html#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access) to get the agent packages into your infrastructure.
2. Download the [puppetlabs-apt][apt], [puppetlabs-inifile][inifile], [puppetlabs-stdlib][stdlib] and [puppetlabs-puppet_agent][puppet_agent] modules from the Puppet Forge, and transfer them your Puppet master.
3. Follow the Puppet agent instructions provided above.












