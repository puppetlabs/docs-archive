---
layout: default
title: "PE 2015.2 » Installing » Additional ActiveMQ Hub and Spokes"
subtitle: "Additional ActiveMQ Hub and Spoke Installation"
canonical: "/pe/latest/install_add_activemq.html"
---

The following guide provides instructions for adding additional ActiveMQ hubs and spokes to large Puppet Enterprise deployments managing more than 1000 agent nodes. Building out your ActiveMQ brokers will provide efficient load balancing of network connections for relaying MCollective messages through your PE infrastructure.

Adding ActiveMQ hubs and spokes can be done in addition to, or independent from, adding [additional Puppet masters](./install_multimaster.html) to large deployments.

For more information about MCollective, refer to the [MCollective docs](/mcollective/index.html) and, more specifically, [ActiveMQ clustering](/mcollective/reference/integration/activemq_clusters.html#activemq-clustering).

Please be sure to review these procedures before beginning, as performing these steps out of order can cause problems for your configuration.

In addition, please note the following about this guide:

- It's assumed all servers are running the same OS and architecture.
- In these procedures, the following hostnames are used, but you will need to replace them to match the hostnames in your infrastructure:
   - **Puppet master**: `<MASTER.EXAMPLE.COM>`.
   - **PE console**: `<CONSOLE.EXAMPLE.COM>`.
   - **ActiveMQ hub**: `<ACTIVEMQ-HUB.EXAMPLE.COM>`.
   - **ActiveMQ broker 1**: `<ACTVIEMQ.SYD.EXAMPLE.COM>`.
   - **ActiveMQ broker 2**: `<ACTIVEMQ.PDX.EXAMPLE.COM>`.
   - **PE agent**: `<AGENT.EXAMPLE.COM>`.

> **Important**: Ensure time is synced across all your nodes.

Note that you can add as many ActiveMQ hubs and spokes and PE agents as necessary; simply repeat the appropriate steps.

### Step 1: Install Puppet Enterprise

1. Follow the [PE split installation documentation](./install_pe_split.html) to install PE.
2. Log in to the console.

### Step 2: Install Puppet Agent on Nodes

In this step, you'll install the Puppet agent on `<ACTIVEMQ-HUB.EXAMPLE.COM>`, `<ACTVIEMQ.SYD.EXAMPLE.COM>`, `<ACTIVEMQ.PDX.EXAMPLE.COM>`, and `<AGENT.EXAMPLE.COM>`. You must perform this step so that each machine has a Puppet agent installed.

**To install the Puppet agent**:

1. SSH into each node and `curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/install.bash | sudo bash -s agent:ca_server=<MASTER.EXAMPLE.COM>`.

   This will install and configure the Puppet agent on the nodes.

2. From the console, locate and sign the certificate request and sign the request for each node.


### Step 3: Create the ActiveMQ Hub Group

In this step, you'll use the PE console to create the ActiveMQ Hub group.

**To create the ActiveMQ Hub group**:

1. From the console, click __Nodes__ > __Classification__.
2. From the __Classification__ page, in the **Node group name** field, name the ActiveMQ Hub group (e.g., `PE ActiveMQ Hub`).
3. From the __Parent name__ drop-down menu, select **PE Infrastructure**.
4. Click __Add group__.
5. Select the __PE ActiveMQ Hub__ group.
6. In the __Node name__ field, enter `<ACTIVEMQ-HUB.EXAMPLE.COM>`.
7. Click __Pin node__.
8. Click the __Classes__ tab.
9. In the __class name__ field, add `puppet_enterprise::profile::amq::hub`.
10. Click __Add class__ .
11. In the __puppet_enterprise::profile::amq::hub__ class, from the __Parameter__ drop-down list, select `network_connector_spoke_collect_tag`, and in the __Value__ field, enter `pe-amq-network-connectors-for-<ACTIVEMQ-HUB.EXAMPLE.COM>`.
12. Click __Commit changes__.
13. SSH into `<ACTIVEMQ-HUB.EXAMPLE.COM>` and run Puppet with `puppet agent -t`.


### Step 4: Add Additional Spokes to ActiveMQ Broker Group

In this step, you'll use the PE console to add `<ACTVIEMQ.SYD.EXAMPLE.COM>` and `<ACTIVEMQ.PDX.EXAMPLE.COM>` to the __PE ActiveMQ Broker__ group, which is configured during the installation of PE.

**To add additional spokes to PE ActiveMQ Broker group**:

1. From the __Classification__ page, click the __PE ActiveMQ Broker__ group.
2. Ensure you've selected the __Rules__ tab.
3. In the __Node name__ field, enter `<ACTVIEMQ.SYD.EXAMPLE.COM>` and `<ACTIVEMQ.PDX.EXAMPLE.COM>`.
4. Click __Pin node__ .
5. Click the __Classes__ tab.
6. In the __puppet_enterprise::profile::amq::broker__ class, from the __Parameter__ drop-down list, select `activemq_hubname`. Enter the Hub’s fully qualifed domain name, e.g. `<ACTIVEMQ-HUB.EXAMPLE.COM>`.
7. Click __Commit change__.
8. Run Puppet on `<ACTVIEMQ.SYD.EXAMPLE.COM>` and `<ACTIVEMQ.PDX.EXAMPLE.COM>`.

   a. SSH into `<ACTVIEMQ.SYD.EXAMPLE.COM>`.

   b. Run `puppet agent -t`.

   c. When the run completes, SSH to `<ACTIVEMQ.PDX.EXAMPLE.COM>`, and run `puppet agent -t`.

9. After you run Puppet on all spoke nodes, run Puppet on each hub node (in this case, `<ACTIVEMQ-HUB.EXAMPLE.COM>`).

   > **Note**: The Puppet master (e.g., `<MASTER.EXAMPLE.COM>`) is, by default, already an MCollective broker. If needed, you can unpin it from the __PE ActiveMQ Broker__ group.

### Step 5: Configure MCollective to Communicate With Additional Spokes

For large scale and/or geo-diverse environments in which ActiveMQ hubs and spokes are used to manage large numbers of MCO subscribers/servers, we recommend one of the following options to classify the ActiveMQ nodes.

In this example, we'll assume a datacenter in Portland, Oregon and one in Sydney, Australia.

The first thing you'll need to do is create a custom fact that can be used to identify the physical attributes of the groups of MCO subscribers/servers in Portland and Sydney. And then, based on your infrastructure needs, choose one of the following options:

- **Option A**: Create new node groups for each spoke or groups of spokes in Portland and Sydney. These groups should inherit the PE MCollective group, and they should include the  `puppet_enterprise::profile::mcollective::agent` class, with the `activemq_brokers` parameter set to the name of the desired spoke(s). The custom fact can be used as the classifying rule for each Node Group.

- **Option B**: If you do not want to use node groups, you can use Hiera and automatic data binding instead. In this case, you'll need to  remove the `mcollective_middleware_hosts parameter` from the `puppet_enterprise` class in the PE Infrastructure group, and place this parameter within Hiera at the appropriate level to distinguish the different Spokes.

#### Create A `data_center` Custom Fact

Please refer to the [Facter 2.4 documentation](/facter/2.4/fact_overview.html) for information about creating and deploying custom facts.

To provide you with a simple example, we will create a static custom fact on a node called `<AGENT.EXAMPLE.COM>` that we want to bind to the AMQ spoke for our Sydney data center `spoke.syd.example.com`.

**To create the custom fact file on `<AGENT.EXAMPLE.COM>`**:

* If `<AGENT.EXAMPLE.COM>`is a *nix machine, run:

  ~~~
  puppet apply -e 'file { ["/etc/puppetlabs", "/etc/puppetlabs/facter", "/etc/puppetlabs/facter/facts.d"]: ensure => directory }'
  puppet apply -e 'file {"/etc/puppetlabs/facter/facts.d/data_center.txt": ensure => file, content => "data_center=syd"}'
  ~~~

* If `<AGENT.EXAMPLE.COM>`is a Windows machine  (Windows Vista, 7, 8, 2008, 2012), run:

  ~~~
  puppet apply -e "file { ['C:/ProgramData/PuppetLabs', 'C:/ProgramData/PuppetLabs/facter', 'C:/ProgramData/PuppetLabs/facter/facts.d']: ensure => directory }"
  puppet apply -e "file {'C:/ProgramData/PuppetLabs/facter/facts.d/data_center.txt': ensure => file, content => 'data_center=syd'}"
  ~~~

**Option A**: Use the PE Console to Create new Node Groups for ActiveMQ Spokes in Sydney and Portland

>**Note**: the node must still belong to the PE MCollective Node Group

1. In the PE console, navigate to the **Classification** page. 
2. In the **Node group name** field, create a new group named for the Sydney datacenter (e.g., **Sydney_datacenter**).
3. From the **Parent name** drop-down list, select PE MCollective. 
4. Click **Add group**. 
5. Select the **Sydney_datacenter** group, and from the **Rules** tab, set the custom fact for the Sydney datacenter.

   a. In the **Fact** field, enter "data_center" (without the quotation marks).
   
   b. From the **Operator** drop-down list, select **is**, and in the **Value** field, enter "syd" (without the quotation marks).
   
   c. Click **Add rule**.

6. Click the **Classes** tab, and in the **Add new class** field, enter `puppet_enterprise::profile::mcollective::agent`, and click **Add class**.
7. From the **Parameter** drop-down list, select `activemq_brokers`.
8. In the **Value** field, add the names of the desired spokes (e.g., ["spoke.syd.example.com"]. Note that this must be entered as an array. 
9. Click **Add parameter**, and click the Commit change button. 
10. Repeat steps 2-10 for the Portland datacenter. 
11. Run Puppet on the ActiveMQ hub and spokes (including the Puppet master) and on any PE agents, or wait for a scheduled run.
12. Continue to [Step 6: Verify Connections](#step-6-verify-connections).

 **Option B**: Use Hiera to Bind Data for  ActiveMQ spokes in Sydney and Portland

  You should use this option if you do not want to create node groups for the ActiveMQ spokes in the PE console. 

>**Note**:  The node group for the hub created in [step 3](#step-3-create-the-activemq-hub-group) is still required.

1. **On the Puppet master**, edit your Hiera config file(`/etc/puppetlabs/code/hiera.yaml`) so that it contains the `data_center` fact as part of the hierarchy. 

   Your Hiera config file should resemble the following:

   ~~~
   #hiera.yaml
      ---
       :backends:
            -  eyaml
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
   
   a. Navigate to  `/etc/puppetlabs/code/environments/production/hieradata/`, and create a file called `syd.yaml`. 
   
   b. Edit `syd.yaml` so that it contains the following content:
   
   ~~~ 
   ---
   puppet_enterprise::profile::mcollective::agent::activemq_brokers:
      - 'spoke.syd.example.com'
   ~~~

   c. Still in the `hieradata` directory, create a file called `pdx.yaml`. 
  
   d. Edit `pdx.yaml` so that it contains the following content:
   
   ~~~ 
   ---
   puppet_enterprise::profile::mcollective::agent::activemq_brokers:
       - 'spoke.pdx.example.com'
   ~~~

3. Verify that Hiera and the custom fact are configured properly.
Verify the custom fact on the end node. On `<AGENT.EXAMPLE.COM>` run `facter data_center`.

   This should return the expected value of `syd` for this example.

4. Verify that Hiera picks up the expected value for ActiveMQ spoke given the appropriate parameters. On the Puppet master, run `hiera puppet_enterprise::profile::mcollective::agent::activemq_brokers data_center=syd environment=production`

   This command should return the expected value of `["spoke.syd.example.com"]` for this example.

5. **On the Puppet master**, restart the pe-puppetserver service.

   ~~~
   puppet resource service pe-puppetserver ensure=stopped
   puppet resource service pe-puppetserver ensure=running
   ~~~
   
   The puppet server will not be able to pick up the updated Hiera hierarchy unless its service has been restarted after changes have been made to this file.

6. Run Puppet on the ActiveMQ hub and spokes (including the Puppet master) and on any PE agents, or wait for a scheduled run.
7. Continue to [Step 6: Verify Connections](#step-6-verify-connections).

### Step 6: Verify Connections

You're just about finished! The following steps will help you verify all connections have been correctly established.

1. To verify the __MCollective__ group is correctly set up, go to `<MASTER.EXAMPLE.COM>` and run `su peadmin` and then `mco ping`.

   You should see the ActiveMQ hub and spokes (including the Puppet master) and any PE agents listed.

2. To verify the ActiveMQ hub's connections are correctly established, go to `<ACTIVEMQ-HUB.EXAMPLE.COM>` and run `netstat -an | grep '61616'`.

   You should see that the ActiveMQ hub has connections set up between the ActiveMQ broker nodes.

> **Tip**: if you need to increase the number of processes the `pe-activemq` user can open/process, refer to [Increasing the ulimit for the `pe-activemq` User](./trouble_orchestration.html#increasing-the-ulimit-for-the-pe-activemq-user) for instructions.


