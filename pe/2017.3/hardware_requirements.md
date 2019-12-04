# Hardware requirements

Before installing Puppet Enterprise, consider the architecture type that best suits your needs. Additionally, we provide hardware requirements based on internal testing.

## Monolithic hardware requirements

In monolithic installations, hardware requirements differ depending on the number of nodes you're managing.

|Node volume|Cores|RAM|/opt/|/var/|EC2|
|-----------|-----|---|-----|-----|---|
|10 or fewer|2|6 GB|20 GB|—|m3.large instance|
|Up to 4,000|16 +|32 + GB|100 GB|10 GB|c4.4xlarge|

To take advantage of progressively larger hardware, you must configure your environment to use additional resources.

**Related topics**  


[Configuring and tuning your Puppet Enterprise infrastructure](config_intro.md#)

[Tuning monolithic installations](tuning_monolithic.md#)

## Monolithic with compile masters hardware requirements

If you are managing more than 4,000 nodes, you can add load-balanced compile masters to your monolithic installation to increase the number of agents you can manage.

Each compile master increases capacity by approximately 1,500–3,000 nodes, until you exhaust the capacity of PuppetDB or the console, which run on the master of masters. If you start to see performance issues around 8,000 nodes, you can adjust your hardware or move to a larger base infrastructure.

**Note:** When you expand your deployment to use compile masters, you must also start using load balancers. It is simpler to upgrade your hardware in your monolithic installation, if you can, than to add compile masters and load balancers.

To manage more than 4,000 nodes, we recommend the following minimum hardware:

|Node volume|Node|Cores|RAM|/opt/|/var/|EC2|
|-----------|----|-----|---|-----|-----|---|
|4,000–20,000|Monolithic node|16|32|150|10|c4.4xlarge|
|Each compile master \(1500 - 3000 nodes\)|4|16|30|2|m3.xlarge|

**Note:** If you need to go beyond 20,000 nodes contact Support or your sales team before setting up a large environment installation.

**Related topics**  


[Configuring and tuning your Puppet Enterprise infrastructure](config_intro.md#)

[Tuning monolithic installations](tuning_monolithic.md#)

## Large environment hardware requirements

A large environment installation is a high-capacity infrastructure. It runs on a split installation with additional compile masters and ActiveMQ message brokering. This installation is suitable for managing more than 20,000 nodes.

With this installation type, you can support more nodes by adding more resources to PuppetDB and increasing the number of compile masters.

|Node volume|Node|Cores|RAM|/opt/|/var/|EC2|
|-----------|----|-----|---|-----|-----|---|
|over 20,000|master|16|32|150|10|c4.4xlarge|
|console|4|16|30|2|m3.xlarge|
|PuppetDB|32|48|200|70|m3.2xlarge|
|compile master|4|16|30|42|m3.xlarge or m4.xlarge|
|ActiveMQ hubs|2|4|10|18|m3.large instance|
|ActiveMQ spokes|2|4|10|18|m3.large|

