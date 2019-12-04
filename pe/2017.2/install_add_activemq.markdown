---
layout: default
title: "ActiveMQ hub and spoke installation"
canonical: "/pe/latest/install_add_activemq.html"
---


You can add ActiveMQ hubs and spokes to large Puppet Enterprise deployments. Building out your ActiveMQ brokers provides efficient load balancing of network connections for relaying MCollective messages through your large PE infrastructure.

For more information about MCollective, refer to the [MCollective docs](/mcollective/index.html) and, more specifically, [ActiveMQ clustering](/mcollective/current/reference/integration/activemq_clusters.html).

Adding ActiveMQ hubs and spokes can be done in addition to, or independent from, adding [additional Puppet masters](./install_multimaster.html) to your PE infrastructure.

See the [PE hardware recommendations](./sys_req_hw.html) for guidance on recommended hardware for large environment installations.

## Install ActiveMQ hubs and spokes

Be sure to review these procedures before beginning, as performing these steps out of order can cause problems for your configuration.

In addition, note the following about this guide:

- It's assumed you've already installed a [monolithic](./install_pe_mono.html) or [split](./install_text_mode_split.html) PE deployment.
- It's assumed all puppet infrastructure servers are running the same OS and architecture.
- In this example, we'll assume we're building a datacenter in Portland, Oregon, and one in Sydney, Australia. We'll use the following hostnames:

   - **Puppet master**: `MASTER.EXAMPLE.COM`
   - **PE console**: `CONSOLE.EXAMPLE.COM`
   - **PE agents**: `AGENT1.EXAMPLE.COM` and `AGENT2.EXAMPLE.COM`
   - **ActiveMQ hub**: `ACTIVEMQ-HUB.EXAMPLE.COM`
   - **ActiveMQ spoke 1**: `ACTVIEMQ.SYD.EXAMPLE.COM`
   - **ActiveMQ spoke 2**: `ACTIVEMQ.PDX.EXAMPLE.COM`

The general procedure proceeds as follows:

1. Install Puppet agent on four nodes.
2. Create the ActiveMQ hub group.
3. Add spokes to the broker group.
4. Create custom fact for node and spoke relationships that can be used to identify the attributes of the groups of MCO subscribers/servers in Portland and Sydney.
5. Classify the ActiveMQ nodes: For large scale and/or geo-diverse environments in which ActiveMQ hubs and spokes are used to manage large numbers of MCO subscribers/servers, we recommend that you classify those nodes in one of two ways:
   - Option 1: Create new node groups for each spoke or groups of spokes in Portland and Sydney.
   - Option 2: If you do not want to use node groups, you can use Hiera and automatic data binding instead.
6. Verify connections in your infrastructure.

### Install Puppet agent on ActiveMQ hub and spoke nodes

Before you can set up your hubs and spokes, you must install Puppet agents on the following nodes:

- `ACTIVEMQ-HUB.EXAMPLE.COM`
- `ACTIVEMQ.SYD.EXAMPLE.COM`
- `ACTIVEMQ.PDX.EXAMPLE.COM`
- `AGENT1.EXAMPLE.COM`
- `AGENT2.EXAMPLE.COM`

You must perform this step so that each machine has a Puppet agent installed.

1. SSH into each machine and run the following command:

   ~~~
   curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/install.bash | sudo bash -s agent:ca_server=<MASTER.EXAMPLE.COM>
   ~~~

2. Use the PE console to [sign their certificate requests](install_agents.html#managing-agent-certificate-requests-from-the-pe-console).

### Create the ActiveMQ hub group

Now that you've installed agents on your hub and broker nodes, you're ready to create the ActiveMQ hub group.

1. In the console, click **Classification**, and then click **Add group**.
2. Specify options for the new node group:

   Option | Value
   ------|------
   Parent name | PE Infrastructure
   Group name | PE ActiveMQ Hub
   Environment | select environment agents are in
   Environment group | **Do not** select this option

3. Click **Add**.
4. Click the link to **add membership rules, classes and variables**.
5. On the **Rules** tab, in the __Node name__ field, enter `ACTIVEMQ-HUB.EXAMPLE.COM`.
6. Click __Pin node__.
7. On the **Classes** tab, in the __class name__ field, add `puppet_enterprise::profile::amq::hub`.
8. Click __Add class__.
9. In the `puppet_enterprise::profile::amq::hub` class, specify parameters:

   - __Parameter__ — select `network_connector_spoke_collect_tag`
   - __Value__ — enter `pe-amq-network-connectors-for-ACTIVEMQ-HUB.EXAMPLE.COM`

10. Commit changes.
11. On `ACTIVEMQ-HUB.EXAMPLE.COM` [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes).

### Add spokes to ActiveMQ Broker group

After you create the hub group, you add your spoke nodes (`ACTIVEMQ.SYD.EXAMPLE.COM` and `ACTIVEMQ.PDX.EXAMPLE.COM`) to the __PE ActiveMQ Broker__ group, which is a pre-configured node group in PE.

**To add additional spokes to PE ActiveMQ broker group**:

1. In the console, click **Classification**, and in the **PE Infrastructure** group, select the __PE ActiveMQ Broker__ group.
2. On the **Rules** tab, in the __Node name__ field, enter `ACTIVEMQ.SYD.EXAMPLE.COM` and `ACTIVEMQ.PDX.EXAMPLE.COM`.
3. Click __Pin node__ .
4. Click the __Classes__ tab.
5. In the __puppet_enterprise::profile::amq::broker__ class and specify parameters:

   - __Parameter__ — select `activemq_hubname`
   - __Value__ — enter `["ACTIVEMQ-HUB.EXAMPLE.COM"]`

   > **Note**: The hub FQDN should be entered as an array. Additional Hubs can be added as needed.

6. Commit changes.
7. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on `ACTIVEMQ.SYD.EXAMPLE.COM` and `ACTIVEMQ.PDX.EXAMPLE.COM`.

8. After you run Puppet on all spoke nodes, run Puppet on each hub node (in this case, `ACTIVEMQ-HUB.EXAMPLE.COM`).

   >**Note**: The Puppet master (e.g., `MASTER.EXAMPLE.COM`) is, by default, already an MCollective broker. If needed, you can unpin it from the __PE ActiveMQ Broker__ group.

### Create a custom fact for node and spoke relationships

By default managed nodes use the Puppet master (master-of-masters) as the ActiveMQ broker.  In a hub and spoke configuration all the nodes other than Puppet infrastructure nodes use the most suitable spoke as their broker.  Suitable spokes are usually those that share a geographic location or share network segments can be due to geo-diversity or different network segments etc.  In some circumstance the Spokes may also be behind a load balancer.

Each node in your infrastructure needs to connect to the most suitable spoke,  and and you can create such connections easily with custom facts..

You'll need to create a custom fact that can be used to identify the physical attributes of the groups of MCO subscribers/servers in Portland and Sydney.

You'll later use the custom fact you'll make (for example, `data_center`) to classify spokes in the console or to bind agents to spokes with Hiera.

In this example, we'll create a custom fact to associate `AGENT.EXAMPLE.COM` with `SPOKE.SYD.EXAMPLE.COM`.

>**Tip:** If needed, refer to the [Facter 2.4 documentation]({{facter}}/fact_overview.html) for information about creating and deploying custom facts.

1. Depending on your operating system, choose one of the following to create the custom fact:

   a. If `AGENT.EXAMPLE.COM`is a *nix machine, run:

       puppet apply -e 'file { ["/etc/puppetlabs", "/etc/puppetlabs/facter", "/etc/puppetlabs/facter/facts.d"]: ensure => directory }'
       puppet apply -e 'file {"/etc/puppetlabs/facter/facts.d/data_center.txt": ensure => file, content => "data_center=syd"}'

   b. If `AGENT.EXAMPLE.COM`is a Windows machine (Windows Vista, 7, 8, 2008, 2012), run:

       puppet apply -e "file { ['C:/ProgramData/PuppetLabs', 'C:/ProgramData/PuppetLabs/facter', 'C:/ProgramData/PuppetLabs/facter/facts.d']: ensure => directory }"
       puppet apply -e "file {'C:/ProgramData/PuppetLabs/facter/facts.d/data_center.txt': ensure => file, content => 'data_center=syd'}"

2. Proceed to one of the following tasks to assign agents to your spokes:

   - [Classify the ActiveMQ spokes with the console](#optional-classify-the-activemq-spokes-with-the-console)
   - [Classify the ActiveMQ spokes with Hiera](#optional-classify-the-activemq-spokes-with-hiera)

### Option 1: Classify the ActiveMQ spokes with the console

Use the PE console to create new node groups for each spoke or groups of spokes in Portland and Sydney. You can use your custom fact as the classifying rule for each node group.

These groups should inherit the PE MCollective group, and they should include the  `puppet_enterprise::profile::mcollective::agent` class, with the `activemq_brokers` parameter set to the name of the desired spoke(s).

>**Note**: The node must still belong to the PE MCollective node group.

1. In the console, click **Classification**, and then click **Add group**.
2. Specify options for the new node group:

   Options  | Value
   -------|-------
   Parent name | PE MCollective
   Group name | Sydney_datacenter
   Environment | Select environment agents are in
   Environment group | **Do not** select this option

3. Click **Add**.
4. Click **Add membership rules, classes, and variables**.
5. On the **Rules** tab, create a rule to add agents to this group:

   Option | Value
   ------|------
   Fact | data_center
   Operator | =
   Value | syd

6. Click **Add rule**, then commit changes.
7. Still in the group you created, click the **Classes** tab, and in the **Add new class** field, enter `puppet_enterprise::profile::mcollective::agent`, and click **Add class**.
8. From the **Parameter** drop-down list, select `activemq_brokers`.
9. In the **Value** field, add the names of the desired spokes (e.g., `["SPOKE.SYD.EXAMPLE.COM"]`).

   >**Note:** The hubs' FQDNS must be entered as an array.

10. Click **Add parameter**, and click the **Commit change** button.
11. Repeat steps 1-10 for the Portland datacenter.
12. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the ActiveMQ hub and spokes (including the Puppet master) and on any PE agents, or wait for a scheduled run.
13. Continue to [verify connections in your infrastructure ](#verify-connections-in-your-infrastructure).

### Option 2: Classify the ActiveMQ spokes with Hiera

If you do not want to use the PE console to create new node groups for each spoke or groups of spokes in Portland and Sydney, you can use Hiera and use automatic data binding instead.

In this case, you'll need to  remove the `mcollective_middleware_hosts parameter` from the `puppet_enterprise` class in the PE Infrastructure group, and place this parameter within Hiera at the appropriate level to distinguish the different spokes.

1. **On the Puppet master**, edit your Hiera config file(`/etc/puppetlabs/puppet/hiera.yaml`) so that it contains the `data_center` fact as part of the hierarchy.

   Your Hiera config file should resemble the following:

   ~~~
   #hiera.yaml
      ---
       :backends:
            - eyaml
            - yaml
       :hierarchy:
            - "%{clientcert}"
            - "%{data_center}"
            - global


        :yaml:
        :datadir: "/etc/puppetlabs/code/environments/%{environment}/hieradata"
   ~~~

2. **On the Puppet master**, add Hiera data files to map the desired ActiveMQ spokes to the `data_center` custom facts.

   The following example commands are for the `syd` and `pdx` datacenters; they assume you're using the `production` environment.

   >**Important**: the name of file you create in this step must match custom fact value.

   a. Navigate to `/etc/puppetlabs/code/environments/production/hieradata/`, and create a file called `syd.yaml`.

   b. Edit `syd.yaml` so that it contains the following content:

   ~~~
   ---
   puppet_enterprise::profile::mcollective::agent::activemq_brokers:
      - 'SPOKE.SYD.EXAMPLE.COM'
   ~~~

   c. Still in the `hieradata` directory, create a file called `pdx.yaml`.

   d. Edit `pdx.yaml` so that it contains the following content:

   ~~~
   ---
   puppet_enterprise::profile::mcollective::agent::activemq_brokers:
       - 'SPOKE.PDX.EXAMPLE.COM'
   ~~~

3. Verify that Hiera and the custom fact are configured properly.
Verify the custom fact on the end node. On `AGENT.EXAMPLE.COM` run `facter data_center`.

   This should return the expected value of `syd` for this example.

4. Verify that Hiera picks up the expected value for ActiveMQ spoke given the appropriate parameters. On the Puppet master, run the following:

   ~~~
   hiera puppet_enterprise::profile::mcollective::agent::activemq_brokers data_center=syd environment=production
   ~~~

   This should return the expected value of `["SPOKE.SYD.EXAMPLE.COM"]` for this example.

5. **On the Puppet master**, reload the pe-puppetserver service.

   ~~~
   sudo service pe-puppetserver reload
   ~~~

   Since Puppet Server doesn't actively monitor the hiera.yaml file for changes, it requires a reload whenever you edit it.

6. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the ActiveMQ hub and spokes (including the Puppet master) and on any PE agents, or wait for a scheduled run.

7. Continue to [verify connections in your infrastructure ](#verify-connections-in-your-infrastructure).

### Verify connections in your infrastructure

The final thing you need to do is verify all connections have been correctly established.

1. To verify the __MCollective__ group is correctly set up, go to `MASTER.EXAMPLE.COM` and run `su peadmin` and then `mco ping`.

   You should see the ActiveMQ hub and spokes (including the Puppet master) and any PE agents listed.

2. To verify the ActiveMQ hub's connections are correctly established, go to `ACTIVEMQ-HUB.EXAMPLE.COM` and run the following command:

   - For RHEL 7 and derivatives: `ss -a -n | grep '61616'`.

   - For other platforms: `netstat -an | grep '61616'`.

   You should see that the ActiveMQ hub has connections set up between the ActiveMQ broker nodes.

> **Tip**: if you need to increase the number of processes the `pe-activemq` user can open/process, refer to [Increasing the ulimit for the `pe-activemq` User](./trouble_orchestration.html#increasing-the-ulimit-for-the-pe-activemq-user) for instructions.






