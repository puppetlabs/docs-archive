# Hardware requirements

These hardware requirements are based on internal testing.

**Tip:** For mid-sized deployments that fall between ranges, you can proportionately scale back the required cores and RAM. For example, internal testing shows that 16 cores and 32 GB RAM accommodates at least 4,000 nodes. You can divide these requirements by 4 to come to 4 cores and 8 GB RAM for 1,000 nodes, or by 2 to come to 8 cores and 16 GB RAM for 2,000 nodes.

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

[Tuning monolithic installations](tuning_monolithic.md#)

## Monolithic hardware requirements

In monolithic installations, hardware requirements differ depending on the number of nodes you're managing.

|Node volume|Cores|RAM|/opt/|/var/|EC2|
|-----------|-----|---|-----|-----|---|
|10 or fewer|2|6 GB|20 GB|—|m5.large|
|Up to 4,000|16 +|32 + GB|100 GB|10 GB|c4.4xlarge|

To take advantage of progressively larger hardware, you must configure your environment to use additional resources.

## Monolithic with compile masters hardware requirements

If you are managing more than 4,000 nodes, you can add load-balanced compile masters to your monolithic installation to increase the number of agents you can manage.

Each compile master increases capacity by approximately 1,500–3,000 nodes, until you exhaust the capacity of PuppetDB or the console, which run on the master of masters. If you start to see performance issues around 8,000 nodes, you can adjust your hardware or move to a larger base infrastructure.

**Note:** When you expand your deployment to use compile masters, you must also start using load balancers. It is simpler to upgrade your hardware in your monolithic installation, if you can, than to add compile masters and load balancers.

To manage more than 4,000 nodes, we recommend the following minimum hardware:

|Node volume|Node|Cores|RAM|/opt/|/var/|EC2|
|-----------|----|-----|---|-----|-----|---|
|4,000–20,000|Monolithic node|16|32|150|10|c4.4xlarge|
|Each compile master \(1500 - 3000 nodes\)|4|8|30|2|m5.xlarge|

