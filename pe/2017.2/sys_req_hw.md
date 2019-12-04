---
layout: default
title: "Hardware requirements"
canonical: "/pe/latest/sys_req_hw.html"
---

Before installing Puppet Enterprise, consider the architecture type that best suits your needs. Additionally, we provide hardware requirements based on internal testing.

## Choosing a PE architecture

There are two base installation types for Puppet Enterprise: monolithic and split. In most cases, a monolithic installation should suit your needs.

- [Monolithic installation](./install_pe_mono.html)(Recommended): The Puppet master, the PE console, and PuppetDB (with PostgreSQL) are all installed on one node. Because all components are on one node, this installation type is easier to install, upgrade, and troubleshoot. You can expand this installation type, and manage up to 20,000 nodes, by adding compile masters to it.

  > The PE AWS Marketplace Image is available for users who are looking to move to AWS, or who are currently running their systems on AWS and want to deploy and manage their Puppet master infrastructure in the cloud. The AWS marketplace image enables you to control the configuration of any number of virtual machines running within the AWS virtual private cloud. The EC2 instance runs applications on the AWS infrastructure and serves as your Puppet master.
  >
  > The Azure Marketplace Image is available for users who are looking to automate the entire lifecycle of their Azure infrastructure. The PE Azure Marketplace Image enables you to manage and control the configuration of any number of virtual machines running on the Azure cloud.

- [Split installation](./install_text_mode_split.html): the Puppet master, the PE console, and PuppetDB (with PostgreSQL) are each installed on separate nodes. You should only use this installation type if you have a limit on the number of cores per server you can have, or if you are managing more than 20,000 nodes and require a large environment installation (LEI).

As your deployment grows, your path will likely go something like this:

1. You’ll install a monolithic installation on one node for managing tens to hundreds of nodes.
2. As you expand and bring more nodes under PE management, you’ll increase the resources available to your monolithic installation by providing more CPUs and RAM.
3. As you expand into managing thousands of nodes, you’ll add compile masters to distribute the agent catalog compilation workload.  You’ll likely reach a steady state if you’re managing less than 8000 nodes.
4. If your deployment grows even larger, you’ll migrate from a monolithic installation to a Large Environment Installation.

## Hardware requirements

We provide the following hardware requirements for Puppet Enterprise. These requirements may vary depending on the size and complexity of your PE infrastructure.

We provide recommendations for the following installation types:

- Monolithic
- Monolithic with compile masters
- Split/LEI

### Monolithic installation hardware requirements

To manage 10 or fewer nodes, we recommend the following minimum hardware:

Node volume |Cores | RAM       | /opt/   | EC2
------------|------ |-----------|---------|------
 10 or fewer|     2  | 6 GB   | 20 GB  | m3.large instance

To manage more nodes, you will need to upgrade your hardware. The default configuration of PE is tested to support up to 4000 nodes. To manage this many nodes, we recommend the following minimum hardware:

Node volume | Cores | RAM       | /opt/   | /var/ | EC2
------------|------|-----------|---------|-------|------
 10 - 4000  |  16 +  | 32 + GB   | 100 GB  | 10 GB | c4.4xlarge

To take full advantage of progressively larger hardware, you’ll need to configure PE to make use of those resources. See our guide to [configuring and tuning your PE infrastructure](./config_intro.html) for more information.

[Tuning monolithic installations (reference)](./config_monolithic.html) provides tuning information for various sizes of monolithic installations.

### Monolithic with compile masters installation hardware requirements

If you are managing more than 4000 nodes, you can add load-balanced compile masters to your monolithic installation to increase the amount of agents you can manage.

Each compile master increases capacity by approximately 1500 - 3000 nodes, until you exhaust the capacity of PuppetDB or the PE console, which run on the MoM. If you start to see performance issues around 8000 nodes, you can adjust your hardware or move to a larger base infrastructure.

>**Note:** When you expand your deployment to use compile masters, you must also start using load balancers. It is simpler to upgrade your hardware in your monolithic installation, if you can, than to add compile masters and load balancers.

To manage more than 4000 nodes, we recommend the following minimum hardware:

<table>
  <tr>
    <th>Node volume</th>
    <th>Node</th>
    <th>Cores </th>
    <th>RAM</th>
    <th>/opt/</th>
    <th>/var/</th>
    <th>EC2</th>
  </tr>
  <tr>
    <td rowspan="2">4000 - 20,000</td>
    <td>Monolithic node</td>
    <td>16</td>
    <td>32</td>
    <td>150</td>
    <td>10</td>
    <td>c4.4xlarge</td>
  </tr>
  <tr>
    <td>Each compile master (1500 - 3000 nodes)</td>
    <td>4</td>
    <td>16</td>
    <td>30</td>
    <td>2</td>
    <td>m3.xlarge</td>
  </tr>
</table>

To take full advantage of progressively larger hardware, you’ll need to configure PE to make use of those resources. See our guide to [configuring and tuning your PE infrastructure](./config_intro.html) for more information.

>**Note**: If you need to go beyond 20,000 nodes contact Puppet support or your sales team before setting up a large environment installation.


### Large environment installation hardware requirements

A large environment installation is a high-capacity PE infrastructure. It runs on a split installation with additional compile masters and ActiveMQ message brokering. This installation is suitable for managing over 20,000 nodes. We recommend, at minimum, the following hardware:

<table>
  <tr>
    <th>Node volume</th>
    <th>Node</th>
    <th>Cores </th>
    <th>RAM</th>
    <th>/opt/</th>
    <th>/var/</th>
    <th>EC2</th>
  </tr>
  <tr>
    <td rowspan="6">over 20,000</td>
    <td>Puppet master</td>
    <td>4</td>
    <td>16</td>
    <td>10</td>
    <td>42</td>
    <td>m3.xlarge or m4.xlarge</td>
  </tr>
  <tr>
    <td>PE console</td>
    <td>4</td>
    <td>4</td>
    <td>30</td>
    <td>22</td>
    <td>m3.xlarge or m4.xlarge </td>
  </tr>
  <tr>
    <td>PuppetDB</td>
    <td>32</td>
    <td>48</td>
    <td>200</td>
    <td>70</td>
    <td>m3.2xlarge </td>
  </tr>
  <tr>
    <td>(3) Compile master</td>
    <td>4</td>
    <td>16</td>
    <td>30</td>
    <td>42</td>
    <td>m3.xlarge or m4.xlarge </td>
  </tr>
  <tr>
    <td>ActiveMQ hubs</td>
    <td>2</td>
    <td>4</td>
    <td>10</td>
    <td>18</td>
    <td>m3.large instance</td>
  </tr>
  <tr>
    <td>ActiveMQ Spoke</td>
    <td>2</td>
    <td>4</td>
    <td>10</td>
    <td>18</td>
    <td>m3.large </td>
  </tr>
</table>

With this installation type, you will be able to support more nodes by adding more resources to PuppetDB and increasing the number of compile masters you have.

