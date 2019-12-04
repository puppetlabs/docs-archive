# Installing ActiveMQ hubs and spokes

You can add hubs and spokes to large Puppet Enterprise deployments for efficient load balancing and for relaying MCollective messages.

Adding hubs and spokes can be done in addition to, or independently from, adding additional masters to your infrastructure.

## Install ActiveMQ hubs and spokes

Setting up hubs and spokes involves classifying nodes into appropriate node groups, and then configuring the connections for those node groups.

### Before you begin

You must have a monolithic or split deployment with all infrastructure servers running the same OS and architecture.

### About this task

### Install the agent on ActiveMQ hub and spoke nodes

Hub and spoke nodes must have an agent installed.

#### About this task

#### Procedure

1.  SSH into each machine that you want to operate as a hub or spoke and install Puppet.

    ```
    
                         `
    curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/install.bash | sudo bash -s agent:ca_server=<MASTER.EXAMPLE.COM>
    `
                      
    ```

2.  Sign the nodes' certificates.


### Create the ActiveMQ hub group

Create the ActiveMQ hub group and pin the hub node to the group.

#### About this task

#### Procedure

1.  In the console, click **Classification**, and then click **Add group**. 

2.  Specify options for the new node group and then click **Add**.

    |Option|Value|
    |------|-----|
    |**Parent name**|**PE Infrastructure**|
    |**Group name**|PE ActiveMQ Hub|
    |**Environment**|Select the environment agents are in.|
    |**Environment group**|**Do not** select this option.|

3.  Click the link to **Add membership rules, classes, and variables**.

4.  On the **Rules** tab in the **Node name** field, enter the hostname for the hub and click **Pin node**. 

5.  On the **Configuration** tab in the **Class name** field, enter **puppet\_enterprise::profile::amq::hub**, and click **Add class**. 

6.  In the **puppet\_enterprise::profile::amq::hub** class, specify parameters and then commit changes. 

    |Parameter|Value|
    |---------|-----|
    |**network\_connector\_spoke\_collect\_tag**|pe-amq-network-connectors-for-<HUB\_HOSTNAME\>|

7.  Run Puppet on the hub node.


### Add spokes to ActiveMQ broker group

Add spoke nodes to the **PE ActiveMQ Broker** group, which is a preconfigured node group.  

#### About this task

#### Procedure

1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE ActiveMQ Broker** group.

2.  On the **Rules** tab in the **Node name** field, enter the hostname for each spoke and click **Pin node**. 

3.  On the **Configuration** tab in the **puppet\_enterprise::profile::amq::broker** class, specify parameters and then commit changes. 

    |Parameter|Value|
    |---------|-----|
    |**activemq\_hubname**|Hub FQDN, entered as an array, for example \["ACTIVEMQ-HUB.EXAMPLE.COM"\].|

4.  Run Puppet on all spoke nodes, and then run Puppet on the hub node.


### Create a custom fact for node and spoke relationships

In a hub and spoke configuration, all the nodes other than infrastructure nodes use the most suitable spoke as their broker. You create these connections with custom facts.

#### About this task

Suitable spokes are usually those that share a geographic location or share network segments. In some circumstance the spokes may also be behind a load balancer.

#### Procedure

1.  Create a custom fact.

    For example, create a custom fact that represents a Sydney data center.

    -   \*nix

```
puppet apply -e 'file { ["/etc/puppetlabs", "/etc/puppetlabs/facter", "/etc/puppetlabs/facter/facts.d"]: ensure => directory }'
puppet apply -e 'file {"/etc/puppetlabs/facter/facts.d/data_center.txt": ensure => file, content => "data_center=syd"}
```

    -   Windows

```
puppet apply -e "file { ['C:/ProgramData/PuppetLabs', 'C:/ProgramData/PuppetLabs/facter', 'C:/ProgramData/PuppetLabs/facter/facts.d']: ensure => directory }"
puppet apply -e "file {'C:/ProgramData/PuppetLabs/facter/facts.d/data_center.txt': ensure => file, content => 'data_center=syd'}"
```


#### Results

### Classify the ActiveMQ spokes

Use custom facts to classify spokes in the console or to bind agents to spokes with Hiera.

#### Classify the ActiveMQ spokes with the console

Use the console to create new node groups for each spoke or group of spokes in your infrastructure.

##### About this task

Groups must belong to the **PE MCollective** group, and include the `puppet_enterprise::profile::mcollective::agent` class, with the `activemq_brokers` parameter set to the name of the desired spokes.

##### Procedure

1.  In the console, click **Classification**, and then click **Add group**. 

2.  Specify options for the new node group, and then click **Add**.

    For example, create a group that represents a Sydney datacenter.

    |Option|Value|
    |------|-----|
    |**Parent name**|**PE MCollective**|
    |**Group name**|Sydney\_datacenter|
    |**Environment**|Select the environment agents are in.|
    |**Environment group**|**Do not** select this option.|

3.  Click the link to **Add membership rules, classes, and variables**.

4.  On the **Rules** tab, create a rule to add agents to this group, then click **Add rule** and commit changes.

    For example, create a rule that matches nodes in the Sydney datacenter.

    |Option|Value|
    |------|-----|
    |**Fact**|**data\_center**|
    |**Operator**|**=**|
    |**Value**|syd|

5.  On the **Configuration** tab in the **Add new class** field, enter **puppet\_enterprise::profile::mcollective::agent** and click **Add class**.

6.  In the **puppet\_enterprise::profile::mcollective agent** class, specify parameters and then commit changes.

    |**Parameter**|**Value**|
    |-------------|---------|
    |**activemq\_brokers**|Names of the spokes you want to classify. Hubs must be entered as an array.|

7.  Run Puppet on the ActiveMQ hub and spokes, including the master or master of masters, and on any agents.


#### Classify the ActiveMQ spokes with Hiera

If you do not want to use the console to create new node groups for each spoke or groups of spokes in your infrastructure, you can use Hiera with automatic data binding instead.

##### About this task

You must remove the `mcollective_middleware_hosts parameter` from the `puppet_enterprise` class in the **PE Infrastructure** group, and place this parameter within Hiera at the appropriate level to distinguish the different spokes.

##### Procedure

1.  On the master, edit your Hiera config file \(`/etc/puppetlabs/puppet/hiera.yaml`\) so that it contains, as part of the hierarchy, the custom fact you're using to classify spokes.

    For example, add the custom data\_center fact to the hierarchy.

    ```
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
    ```

2.  On the master, add Hiera data files to map ActiveMQ spokes to custom facts.

    For example, map spokes to Sydney and Portland datacenters using the custom data\_center fact.

    1.  Navigate to `/etc/puppetlabs/code/environments/production/hieradata/`, and create a file called `syd.yaml` that contains the content:

```
---
puppet_enterprise::profile::mcollective::agent::activemq_brokers:
   - 'SPOKE.SYD.EXAMPLE.COM'

```

    2.  Still in the `hieradata` directory, create a file called `pdx.yaml` that contains the content:

```
---
puppet_enterprise::profile::mcollective::agent::activemq_brokers:
    - 'SPOKE.PDX.EXAMPLE.COM'
```

3.  Verify the custom fact on the end node.

    For example, running `facter data_center` on a node classified in the Sydney datacenter returns the value syd. 

4.  Verify that Hiera picks up the expected value for the ActiveMQ spoke given the appropriate parameters.

    For example, on the master, running this command returns the value of the Sydney broker hostname.

    ```
    hiera puppet_enterprise::profile::mcollective::agent::activemq_brokers data_center=syd environment=production
    ```

5.  On the master, reload the pe-puppetserver service: `sudo service pe-puppetserver reload`.

6.  Run Puppet on the ActiveMQ hub and spokes, including the master or master of masters, and on any agents.


### Verify connections in your infrastructure

Check that ActiveMQ hub and spokes are configured correctly.

#### About this task

#### Procedure

1.  Verify that the **MCollective** group is correctly configured by accessing the master hostname and running `su peadmin` and then `mco ping`.

    The hub and spokes, including the master and any agents, are listed.

2.  Verify that the hub's connections are correctly established. Access the ActiveMQ hub hostname and run:

    -   RHEL 7 and derivatives — `ss -a -n | grep '61616'`

    -   Other platforms — `netstat -an | grep '61616'`

    The hub displays connections to ActiveMQ broker nodes.


