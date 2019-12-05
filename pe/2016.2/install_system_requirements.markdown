---
layout: default
title: "System requirements and pre-installation"
canonical: "/pe/latest/install_system_requirements.html"
---

Before installing Puppet Enterprise:

* Ensure that your nodes are running a supported operating system.
* Ensure that your Puppet master and console servers are sufficiently powerful (see the [hardware section](#hardware-recommendations) below).
* Ensure that your network, firewalls, and name resolution are configured correctly and all target servers are communicating.
* Plan to install the Puppet master server before the console server, and the console server before any agent nodes. If you are separating components, install them in this order:
    1. Puppet Master
    2. PuppetDB and PostgreSQL
    3. Console
    4. Agents

## Supported operating systems

Puppet Enterprise runs on the following platforms:

### Puppet master platforms

<table>
  <tr>
    <th>Operating system</th>
    <th>Versions</th>
    <th>Architecture</th>
  </tr>
  <tr>
    <td>Red Hat Enterprise Linux</td>
    <td>6, 7</td>
    <td>x86_64</td>
  </tr>
  <tr>
    <td>CentOS</td>
    <td>6, 7</td>
    <td>x86_64</td>
   </tr>
  <tr>
    <td>Oracle Linux</td>
    <td>6, 7</td>
    <td>x86_64</td>
   </tr>
  <tr>
    <td>Scientific Linux</td>
    <td>6, 7</td>
    <td>x86_64</td>
   </tr>
  <tr>
    <td>SUSE Linux Enterprise Server</td>
    <td>11 (SP 1 and later), 12</td>
    <td>x86_64</td>
   </tr>
   <tr>
    <td>Ubuntu</td>
    <td>12.04, 14.04, 16.04</td>
    <td>x86_64</td>
   </tr>
</table>

### Puppet agent platforms

<table>
  <tr>
    <th>Operating system</th>
    <th>Versions</th>
    <th>Architecture</th>
  </tr>
  <tr>
    <td>Red Hat Enterprise Linux</td>
    <td>4, 5, 6, 7</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386 for 5, 6</li>
        <li>IBM z System for 6, 7</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>CentOS</td>
    <td>5, 6, 7</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386 for 5, 6</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Oracle Linux</td>
    <td>5, 6, 7</td>
    <td>
     <ul>
        <li>x86_64</li>
        <li>i386 for 5, 6</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Scientific Linux</td>
    <td>5, 6, 7</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386 for 5, 6</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>SUSE Linux Enterprise Server</td>
    <td>10 (SP 4 only), 11 (SP 1 and later), 12</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386 for 10, 11</li>
        <li>IBM z System for 11, 12</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Solaris</td>
    <td>10 (update 9 or later), 11</td>
    <td>
      <ul>
        <li>SPARC</li>
        <li>i386</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Ubuntu</td>
    <td>10.04, 12.04, 14.04, 16.04</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Fedora</td>
    <td>22, 23, 24</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
       </ul>
    </td>
  </tr>
  <tr>
    <td>Debian</td>
    <td>Wheezy (7), Jessie (8)</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
       </ul>
     </td>
   </tr>
  <tr>
    <td>Microsoft Windows (Server OS)</td>
    <td>2008, 2008R2, 2012, 2012R2, 2012R2 core</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>Microsoft Windows (Consumer OS)</td>
    <td>Vista, 7, 8, 8.1, 10</td>
    <td>
      <ul>
        <li>x86_64</li>
        <li>i386</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td>OS X</td>
    <td>10.9, 10.10, 10.11</td>
    <td style="text-align:center">x86_64</td>
  </tr>
  <tr>
    <td>AIX</td>
    <td>5.3, 6.1, 7.1</td>
    <td style="text-align:center">Power</td>
   </tr>
</table>

>**Note**: Some operating systems require an active subscription with the vendor's package management system, such as the Red Hat Network, to install dependencies.

### Supported network devices

Puppet Enterprise supports running Puppet agents on the following networking devices/operating systems:

- **Arista EOS**: Refer the [aristanetworks/netdev_stdlib_eos module](https://forge.puppetlabs.com/aristanetworks/netdev_stdlib_eos#limitations) on the Puppet Forge for system requirements and dependencies.
- **Cumulus Linux**: Refer to the [Cumulus Linux page](https://forge.puppetlabs.com/cumuluslinux/) on the Puppet Forge for system requirements and dependencies.
- **Cisco NS-OX EOS**: Refer to the [Cisco Puppet page](https://forge.puppet.com/puppetlabs/ciscopuppet/readme) on the Puppet Forge for system requirements and dependencies.
- **Cisco IOS-XR**: Refer to the [Cisco Puppet page](https://forge.puppet.com/puppetlabs/ciscopuppet) on the Puppet Forge for system requirements and dependencies.
- **Huawei CloudEngine**: Refer to [Installing Huawei CloudEngine Agents](./install_huawei_ce.html) for installation instructions.

## Operating system support life cycles

### Puppet agent and operating system support life cycles

Puppet ends platform-specific support for Puppet agent at the platform vendor’s end-of-life (EOL) date.  

For example, Fedora 20 reached its EOL on June 23, 2015. As of that date, Puppet stopped testing and providing fixes, updates, and support for Puppet Enterprise and open source Puppet versions of that agent. 

On platforms where the vendor does not publish EOL dates, we support Puppet agent on the two most recent releases of a platform (e.g Mac OS X 10.10 and 10.11). (When Mac OS 10.12 launches, we will send a notice of the deprecation plan for Mac OS X 10.10). 

Puppet may choose to continue to support certain agent operating systems after the platform vendor’s EOL solely at our discretion.

In all cases, we will announce a Puppet agent platform’s EOL at least 90 days before Puppet’s EOL date for the platform.

### PE master and operating system support life cycles

Puppet aligns Puppet Enterprise master platform support to the [PE version support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle).
In general, Puppet supports a Puppet Enterprise master platform until the [EOL date](https://puppet.com/misc/puppet-enterprise-lifecycle) for a Puppet Enterprise version.

However, if a PE master platform’s vendor EOL date is more than 6 months sooner than its PE Version’s EOL date, we may, at our discretion, end support for that master platform at the platform vendor’s EOL date. 

In all cases, we will announce a PE master platform’s EOL at least 6 months before Puppet’s EOL date for the platform. 

### Upcoming Platform End-of-life (EOL)

In accordance with our operating system [support lifecycle policy](https://puppet.com/misc/puppet-enterprise-lifecycle), platform-specific support will end on the specified EOL dates, as listed below.

At EOL, Puppet will stop providing agents, masters, patches, fixes and security updates for the operating system.

<table>
 <tr>
   <th>Operating system</th>
   <th>PE agent and/or master</th>
   <th>OS vendor's EOL</th>
   <th>Puppet's EOL</th>
  </tr>
  <tr>
   <td>Fedora 22</td>
   <td>Agent</td>
   <td>7/19/2016</td>
   <td>11/30/2016</td>
  </tr>
  <tr>
   <td>Mac OS X 10.9</td>
   <td>Agent</td>
   <td></td>
   <td>11/30/2016</td>
  </tr> 
  <tr>
   <td>Ubuntu 15.10 (Wily)</td>
   <td>Agent</td>
   <td>7/28/2016</td>
   <td>11/30/2016</td>
  </tr>
  <tr>
   <td>Ubuntu 10.04 (Lucid)</td>
   <td>Agent</td>
   <td>4/30/2015</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>Ubuntu 12.04 (Precise)</td>
   <td>Agent, Master</td>
   <td>April 2017</td>
   <td>4/30/2017</td>
  </tr>
  <tr>
   <td>Windows Server 2003</td>
   <td>Agent for 3.8 (not >=2015.2)</td>
   <td>7/14/2015</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>Debian 6 (Squeeze)</td>
   <td>Agent and Master for 3.8 (not >=2015.2)</td>
   <td>2/29/2016</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>SLES 10</td>
   <td>Agent</td>
   <td>7/31/2016</td>
   <td>12/31/2016</td>
  </tr>
  <tr>
   <td>EL (RHEL, CentOS, Scientific Linux, Oracle Linux) 4</td>
   <td>Agent</td>
   <td>3/31/2017</td>
   <td>3/31/2017</td>
  </tr>
  <tr>
   <td>AIX 5.3</td>
   <td>Agent</td>
   <td>4/30/2012</td>
   <td>6/30/2017</td>
  </tr>
 </table> 
  

## Performing major OS upgrades when Puppet Enterprise is installed

In this context, a major OS upgrade refers to an upgrade to a new whole version, such as an upgrade from CentOS 6.0 to CentOS 7.0; it does not refer to a minor version upgrade (e.g., CentOS 6.5 to CentOS 6.6). A major OS upgrade typically requires [a new version of PE](https://puppetlabs.com/misc/pe-files).

Performing major upgrades of your OS while PE is installed can cause problems with PE. To perform an OS upgrade, you’ll need to perform the following steps:

1. [Back up](./maintain_backup_restore.html#back-up-your-database-and-puppet-enterprise-files) your databases and other PE files.
2. Perform a complete [uninstall](./install_uninstalling.html) (including the -p -d uninstaller option).
3. Upgrade your OS.
4. [Install PE](/pe/latest/install_basic.html).
5. [Restore](./maintain_backup_restore.html#restore-your-database-and-puppet-enterprise-files) your backup.


## Choosing a PE architecture

There are two installation types for Puppet Enterprise:

- [Monolithic installation](./install_pe_mono.html): the Puppet master, the PE console, and PuppetDB (with PostgreSQL) are all installed on one node. Because all components are on one node, this installation type is easier to install, upgrade, and troubleshoot. You can expand this installation type by adding compile masters to it.
- [Split installation](./install_pe_split.html): the Puppet master, the PE console, and PuppetDB (with PostgreSQL) are each installed on separate nodes. You should only use this installation type if you have a limit on the number of cores per server you can have, or if you are running 8,000+ nodes.


A monolithic installation is appropriate for managing up to 2000 nodes. As you approach this number of managed nodes,  you can scale your installation by [adding compile masters](./install_multimaster.html). Each compile master will allow you to manage approximately another 1500 nodes in your infrastructure, and you can continue adding compile masters until it starts causing performance degradation with PuppetDB or the PE console. Such performance issues typically occur around 8000 nodes.

As your deployment grows, your path will likely go something like this:

1. You’ll install a monolithic installation on one node for managing tens to hundreds of nodes.
2. As you expand and bring more nodes under PE management, you’ll increase the resources available to your monolithic installation by providing more CPUs and RAM.
3. As you expand into managing thousands of nodes, you’ll add compile masters to distribute the agent catalog compilation workload.  You’ll likely reach a steady state if you’re managing less than 8000 nodes.
4. If your deployment grows even larger, you’ll migrate from a monolithic installation to a Large Environment Installation.

## Hardware recommendations

We provide the following hardware recommendations for Puppet Enterprise, but please note these recommendations may vary depending on the size and complexity of your PE infrastructure.

[Tuning monolithic installations (reference)](./config_monolithic.html) provides tuning information for various sizes of monolithic installations.

### Monolithic installation

To manage 10 or fewer nodes, we recommend the following minimum hardware:

Node volume |Cores | RAM       | /opt/   | EC2
------------|------ |-----------|---------|------
 10 or fewer|     2  | 6 GB   | 20 GB  | m3.xlarge instance

To manage more nodes you will need to upgrade your hardware. The default configuration of PE is tested to support up to 4000 nodes. To manage this many nodes, we recommend the following minimum hardware:

Node volume | Cores | RAM       | /opt/   | /var/ | EC2
------------|------|-----------|---------|-------|------
 10 - 4000  |  16 +  | 32 + GB   | 100 GB  | 10 GB | m3.xlarge or c4.4xlarge

To take full advantage of progressively larger hardware, you’ll need to configure PE to make use of those resources. See our guide to [configuring and tuning your PE infrastructure](./config_intro.html) for more information.

### Monolithic plus compile masters installation

**Note:** When you expand your deployment to use compile masters, you must also start using load balancers. It is simpler to upgrade your hardware in your monolithic installation, if you can, than to add compile masters and load balancers.

If you are managing more than 4000 nodes, you can add load-balanced compile masters to your monolithic installation to increase the amount of agents you can manage. Each compile master increases capacity by approximately 1500 - 3000 nodes, until you exhaust the capacity of PuppetDB or the PE console, which run on the MoM. If you start to see performance issues around 8000 nodes, you can adjust your hardware or move to a larger base infrastructure.

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


### Large environment installation

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



## Supported browsers

The following browsers are supported for use with the PE console:

* Google Chrome: Current version, as of release
* Mozilla Firefox: Current version, as of release
* Microsoft Edge: Current version, as of release
* Microsoft Internet Explorer: 10 and 11
* Apple Safari: 9


## System configuration

Before installing Puppet Enterprise at your site, you should make sure that your nodes and network are properly configured.

### Set umask for install/upgrades

To prevent potential failures, you should set a umask value of 0022 on your Puppet master.

### Timekeeping

We recommend using NTP or an equivalent service to ensure that time is in sync between your Puppet master and any Puppet agent nodes. If time drifts out of sync in your PE infrastructure, you may encounter issues such as nodes disappearing from live manangement in the console. A service like NTP ([available as a Puppet supported module](https://forge.puppetlabs.com/puppetlabs/ntp)) will ensure accurate timekeeping.

### Name resolution

* Decide on a preferred name or set of names agent nodes can use to contact the Puppet master server.
* Ensure that the Puppet master server can be reached via domain name lookup by all of the future Puppet agent nodes at the site.

You can also simplify configuration of agent nodes by using a CNAME record to make the Puppet master reachable at the hostname `puppet`. (This is the default Puppet master hostname that is automatically suggested when installing an agent node.)

### Firewall configuration

[mono_port_diagram]: ./images/mono_port_diagram.svg
[split_port_diagram]: ./images/split_port_diagram.svg
[lei_port_diagram]: ./images/lei_port_diagram.svg
[mono_port_compile]: ./images/mono_port_compile.svg


Configure your firewalls to accommodate Puppet Enterprise's network traffic. The following diagrams show port usages for standard PE installations. A separate list provides [additional port usage information](#additional-port-usage-for-all-installation-types).

#### For monolithic installs

<a href="./images/mono_port_diagram.png"><img src="./images/mono_port_diagram.png" alt="Monolithic Port Diagram" title="Click to enlarge"> (Click to enlarge)</a>

<table>
  <tr>
    <th>Port</th>
    <th>Use</th>
  </tr>
  <tr>
    <td>8140</td>
    <td>
     <ul>
      <li>The Puppet master uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>The PE console sends request to the Puppet master on this port.</li>
      <li>Certificate requests are passed over this port unless ca_port is set differently.</li>
      <li>Puppet Server <a href="https://docs.puppet.com/pe/latest/status_api.html#puppet-services-status-check">status checks</a> are sent over this port.</li>
      <li>Classifier group: “PE Master”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>443</td>
    <td>
     <ul>
      <li>This port provides host access to the PE console.</li>
      <li>The PE Console accepts HTTPS traffic from end-users on this port.</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
   </td>
  </tr>
  <tr>
    <td>61613</td>
    <td>
     <ul>
      <li>MCollective uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>Any host used to invoke commands must be able to reach MCollective on this port.</li>
      <li>Classifier group: “PE ActiveMQ Broker”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>8142</td>
    <td>
     <ul>
      <li>Orchestration services and the Run Puppet button use this port to accept inbound traffic/responses from Puppet agents (via the PXP agent/PCP broker).</li>
      <li>Classifier group: “PE Orchestrator”</li>
     </ul>
    </td>
  </tr>
</table>

#### For monolithic installs with compile masters

<a href="./images/mono_compile_port_diagram.png"><img src="./images/mono_compile_port_diagram.png" alt="Monolithic with Compile Masters Port Diagram" title="Click to enlarge"> (Click to enlarge)</a>

<table>
  <tr>
    <th>Port</th>
    <th>Use</th>
  </tr>
  <tr>
    <td>8140</td>
    <td>
     <ul>
      <li>The Puppet master uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>The PE console sends request to the Puppet master on this port.</li>
      <li>Certificate requests are passed over this port unless ca_port is set differently.</li>
      <li>Puppet Server <a href="https://docs.puppet.com/pe/latest/status_api.html#puppet-services-status-check">status checks</a> are sent over this port.</li>
      <li>Classifier group: “PE Master”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>443</td>
    <td>
     <ul>
      <li>This port provides host access to the PE console.</li>
      <li>The PE Console accepts HTTPS traffic from end-users on this port.</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
   </td>
  </tr>
  <tr>
    <td>61613</td>
    <td>
     <ul>
      <li>MCollective uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>Any host used to invoke commands must be able to reach MCollective on this port.</li>
      <li>Classifier group: “PE ActiveMQ Broker”</li>
     </ul>
    </td>
  </tr>
    <tr>
    <td>4433</td>
    <td>
     <ul>
      <li>This port is used as a Classifier / Console Services API endpoint.</li>
      <li>The Puppet master needs to be able to talk to the PE console over this port.</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
    </td>
  </tr>
   <tr>
   <td>8081</td>
    <td>
     <ul>
      <li>PuppetDB accepts traffic/requests on this port.</li>
      <li>The Puppet master and PE console send traffic to PuppetDB on this port.</li>
      <li>PuppetDB <a href="https://docs.puppet.com/pe/latest/status_api.html#puppet-services-status-check">status checks</a> are sent over this port.</li>
      <li>Classifier group: “PE PuppetDB”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>8142</td>
    <td>
     <ul>
      <li>Orchestration services and the Run Puppet button use this port to accept inbound traffic/responses from Puppet agents (via the PXP agent/PCP broker).</li>
      <li>Classifier group: “PE Orchestrator”</li>
     </ul>
    </td>
  </tr>
</table>



#### For split installs

<a href="./images/split_port_diagram.png"><img src="./images/split_port_diagram.png" alt="Split Port Diagram" title="Click to enlarge"> (Click to enlarge)</a>

<table>
  <tr>
    <th>Port</th>
    <th>Use</th>
  </tr>
  <tr>
    <td>8140</td>
    <td>
     <ul>
      <li>The Puppet master uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>The PE console sends request to the Puppet master on this port.</li>
      <li>Certificate requests are passed over this port unless ca_port is set differently.</li>
      <li>Puppet Server <a href="https://docs.puppet.com/pe/latest/status_api.html#puppet-services-status-check">status checks</a> are sent over this port.</li>
      <li>Classifier group: “PE Master”</li>
     </ul>
    </td>
  </tr>
  <tr>
  <tr>
    <td>443</td>
    <td>
     <ul>
      <li>This port provides host access to the PE console.</li>
      <li>The PE Console accepts HTTPS traffic from end-users on this port.</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
   </td>
  </tr>
    <td>8081</td>
    <td>
     <ul>
      <li>PuppetDB accepts traffic/requests on this port.</li>
      <li>The Puppet master and PE console send traffic to PuppetDB on this port.</li>
      <li>PuppetDB <a href="https://docs.puppet.com/pe/latest/status_api.html#puppet-services-status-check">status checks</a> are sent over this port.</li>
      <li>Classifier group: “PE PuppetDB”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>61613</td>
    <td>
     <ul>
      <li>MCollective uses this port to accept inbound traffic/requests from Puppet agents.</li>
      <li>Any host used to invoke commands must be able to reach MCollective on this port.</li>
      <li>Classifier group: “PE ActiveMQ Broker”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>5432</td>
    <td>
     <ul>
      <li>PostgreSQL runs on this port.</li>
      <li>The PE console node will need to connect to the PuppetDB node hosting the PostgreSQL database on this port.</li>
      <li>Classifier group: “PE PuppetDB”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>4433</td>
    <td>
     <ul>
      <li>This port is used as a Classifier / Console Services API endpoint.</li>
      <li>The Puppet master needs to be able to talk to the PE console over this port.</li>
      <li>Classifier group: “PE Console”</li>
     </ul>
    </td>
  </tr>
  <tr>
    <td>61616</td>
    <td>
     <ul>
      <li>This port is used for ActiveMQ hub and spoke communication.</li>
      <li>Classifier group: “PE ActiveMQ Broker”</li>
     </ul>
   </td>
  </tr>
  <tr>
    <td>8142</td>
    <td>
     <ul>
      <li>Orchestration services and the Run Puppet button use this port to accept inbound traffic/responses from Puppet agents (via the PXP agent/PCP broker).</li>
      <li>Classifier group: “PE Orchestrator”</li>
     </ul>
    </td>
  </tr>
</table>


#### For large environment installations

See the split installation port/use table for explanations of the ports and their uses.

<a href="./images/lei_port_diagram.png"><img src="./images/lei_port_diagram.png" alt="LEI Port Diagram" title="Click to enlarge"> (Click to enlarge)</a>


> #### Additional port usage for all installation types
>
>- Port **3000**: If you are installing PE using the web-based installer, ensure port **3000** is open. You can close this port when the installation is complete. If necessary, [instructions for port forwarding to the web-based installer](./install_pe_mono.html#port-forwarding-to-the-installer) are available in the installation instructions. (This applies to both split and mono installs.)
>
>- Port **8143**: The orchestrator client uses this port to communicate with the orchestration services running on the Puppet master. If you [install the client on a workstation](./install_pe_client_tools.html#installing-pe-client-tools), this port must be available.
>
>- Port **8150** and **8151**: Razor uses port 8150 for HTTP and 8151 for HTTPS. Any node classified as a Razor server must be able to use these ports.
>
>- Port **4432**: Local connections for node classifier, activity service, and RBAC [status checks](./status_api.html#puppet-services-status-check) are sent over this port. Remote connections should use port **4433**.
>
>- Port **8170**: If you use Code Manager, it requires this port. Code manager [status checks](./status_api.html#puppet-services-status-check) are sent over this port.

## External PostgreSQL Option and Prep Notes

There is no requirement to provide a PostgreSQL database, but if you plan to use your own instance of PostgreSQL (as opposed to the instance that PE installs and manages), review the following notes. Your external PostgreSQL instance should be 9.4.

>**Note:** The [`pe.conf` parameter reference](./install_pe_conf_param.html#external-postgresql-parameters) contains required parameters you must add when performing an install or upgrade with an external PostgreSQL instance.

Refer to [Enabling SSL for PE and external PostgreSQL](./install_ssl_postgresql.html) for additional steps you’ll need to take to ensure SSL is correctly configured between PE and your PostgreSQL database.

1. You must create databases for RBAC, activity service, and the node classifier before installing. The SQL commands to create the databases **resemble** the following:

   ~~~
   CREATE TABLESPACE "pe-orchestrator" LOCATION '/opt/puppetlabs/server/data/postgresql/orchestrator';
   CREATE USER "pe-orchestrator" PASSWORD 'password';
   CREATE DATABASE "pe-orchestrator" OWNER "pe-orchestrator" TABLESPACE "pe-orchestrator" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

   CREATE TABLESPACE "pe-puppetdb" LOCATION '/opt/puppetlabs/server/data/postgresql/puppetdb';
   CREATE USER "pe-puppetdb" PASSWORD 'password';
   CREATE DATABASE "pe-puppetdb" OWNER "pe-puppetdb" TABLESPACE "pe-puppetdb" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

   CREATE TABLESPACE "pe-activity" LOCATION '/opt/puppetlabs/server/data/postgresql/activity';
   CREATE USER "pe-activity" PASSWORD 'password';
   CREATE DATABASE "pe-activity" OWNER "pe-activity" TABLESPACE "pe-activity" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

   CREATE TABLESPACE "pe-classifier" LOCATION '/opt/puppetlabs/server/data/postgresql/classifier';
   CREATE USER "pe-classifier" PASSWORD 'password';
   CREATE DATABASE "pe-classifier" OWNER "pe-classifier" TABLESPACE "pe-classifier" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

   CREATE TABLESPACE "pe-rbac" LOCATION '/opt/puppetlabs/server/data/postgresql/rbac';
   CREATE USER "pe-rbac" PASSWORD 'password';
   CREATE DATABASE "pe-rbac" OWNER "pe-rbac" TABLESPACE "pe-rbac" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;
   ~~~

2. You must enable the [citext extension](http://www.postgresql.org/docs/9.4/static/citext.html) on the RBAC database. To do so, install the `postgresql-contrib` package, and then from inside the RBAC database, run the following commands:

   ~~~
   sudo -u postgres sh
   psql pe_rbac -c 'create extension citext'
   exit
   ~~~

3. Install the [`pg_trgm` extension](http://www.postgresql.org/docs/9.4/static/pgtrgm.html) on the PuppetDB database. This may require the `postgresql-contrib` (or equivalent) package depending on your distribution. To install the extension, run the following commands:

   ~~~
   sudo -u postgres sh
   psql puppetdb -c 'create extension pg_trgm'
   exit
   ~~~

4. Install the [`pgcrypto` extension](http://www.postgresql.org/docs/9.4/static/pgcrypto.html) on the PuppetDB database. To install the extension, run the following commands:

   ~~~
   sudo -u postgres sh
   psql puppetdb -c 'create extension pgcrypto'
   exit
   ~~~

5. Set your PostgreSQL `max_connections` setting to 200.

Consult the [PostgreSQL documentation](http://www.postgresql.org/docs/) for more info.

## Dependencies and OS-specific details

This section details the packages that are installed from the various OS repos.  Unless you do not have internet access, you shouldn't need to worry about installing these manually, they will be set up during PE installation.

### Centos

 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes
-------------|:---------:|:------------:|:-------------:|:------------------------:
pciutils     | x         |              |               |
system-logos | x         |              |               |
which        | x         |              |               |
libxml2      | x         |              |               |
dmidecode    | x         |              |               |
net-tools    | x         |              |               |
curl         |           | x            | x             |
mailcap      |           | x            | x             |
libjpeg      |           | x            |               | x
libtool-ltdl |           | x            | x             |
unixODBC     |           | x            | x             |
libxslt      |           |              |               | x
zlib         | x         |              |               |


### RHEL

 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes |
-------------|:---------:|:------------:|:-------------:|:------------------------:|
pciutils     | x         |              |               |                          |
system-logos | x         |              |               |                          |
which        | x         |              |               |                          |
libxml2      | x         |              |               |                          |
dmidecode    | x         |              |               |                          |
net-tools    | x         |              |               |                          |
cronie (RHEL 6, 7) | x      |              |               |                          |
vixie-cron (RHEL 4, 5) | x |            |               |                          |
curl         |           | x            | x             |                          |
mailcap      |           | x            | x             |                          |
libjpeg      |           | x            |               | x                        |
libtool-ltdl (RHEL 7) |  | x            | x             |                          |
unixODBC (RHEL 7) |      | x            | x             |                          |
libxslt      |           |              |               | x                        |
zlib         | x         |              |               |                          |
gtk2         |           | x            |               |                          |

### SLES

 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes |
-------------|:---------:|:------------:|:-------------:|:------------------------:|
pciutils     | x         |              |               |                          |
pmtools      | x         |              |               |                          |
cron         | x         |              |               |                          |
libxml2      | x         |              |               |                          |
net-tools    | x         |              |               |                          |
libxslt      | x         | x            |               | x                         |
curl         |           | x            | x             |                          |
libjpeg      |           | x            |               | x                        |
db43         |           | x            | x             |                          |
unixODBC     |           | x            | x             |                          |
zlib         | x         |              |               |                          |

### Ubuntu


 &nbsp;      | All Nodes | Master Nodes | Console Nodes | Console/Console DB Nodes |
-------------|:---------:|:------------:|:-------------:|:------------------------:|
pciutils     | x         |              |               |                          |
dmidecode    | x         |              |               |                          |
cron         | x         |              |               |                          |
libxml2      | x         |              |               |                          |
hostname     | x         |              |               |                          |
libldap-2.4-2 | x        |              |               |                          |
libreadline5 | x         |              |               |                          |
file         |           | x            | x             |                          |
libmagic1    |           | x            | x             |                          |
libpcre3     |           | x            | x             |                          |
curl         |           | x            | x             |                          |
perl         |           | x            | x             |                          |
mime-support |           | x            | x             |                          |
libcap2      |           | x            | x             |                          |
libjpeg62    |           | x            |               | x                        |
libxslt1.1   |           |              |               | x                         |
libgtk2.0-0  |           | x            | x             | x                        |
ca-certificates-java |   | x            | x             | x                        |
openjdk-7-jre-headless* | | x            | x             | x                        |
libossp-uuid16 |         | x            | x             | x                        |
zlib         | x         |              |               |                          |

*For Ubuntu 10.04 and Debian 6, use openjdk-6-jre-headless.


### AIX

**Before installing the Puppet agent on AIX systems,** install the following packages on AIX systems. 

These packages are required to **install and run** the Puppet agent on AIX systems.  

* bash
* zlib
* readline
* curl
* OpenSSL

> **Warning**: For cURL and OpenSSL, you must use the versions provided by the "AIX Toolbox Cryptographic Content" repository, which is available via IBM support. Note that the cURL version must be 7.9.3. Do not use the cURL version in the AIX toolbox package for Linux applications, as that version does not include support for OpenSSL.

To install the bash, zlib, and readline packages on your selected node directly, you can run `rpm -Uvh` with the following URLs (note that the RPM package provider on AIX must be run as root):

 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/bash/bash-3.2-1.aix5.2.ppc.rpm
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/zlib/zlib-1.2.3-4.aix5.2.ppc.rpm
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-6.1-1.aix6.1.ppc.rpm (AIX 6.1 and 7.1 *only*)
 * ftp://ftp.software.ibm.com/aix/freeSoftware/aixtoolbox/RPMS/ppc/readline/readline-4.3-2.aix5.1.ppc.rpm (AIX 5.3 *only*)

If you are behind a firewall or running an http proxy, the above commands may not work. Instead, use the [AIX toolbox packages](http://www-03.ibm.com/systems/power/software/aix/linux/toolbox/alpha.html) download available from IBM.

GPG verification will not work on AIX, the RPM version used by AIX (even 7.1) is too old. The AIX package provider doesn't support package downgrades (installing an older package over a newer package). Avoid using leading zeros when specifying a version number for the AIX provider (i.e., use `2.3.4` not `02.03.04`).

The PE AIX implementation supports the NIM, BFF, and RPM package providers. Check the [type reference]({{puppet}}/type.html#package) for technical details on these providers.

### Solaris

Solaris support is agent only.

For Solaris 10, the following packages are required:

  * SUNWgccruntime
  * SUNWzlib
  * In some instances, bash may not be present on Solaris systems. It needs to be installed before running the PE installer. Install it via the media used to install the OS or via CSW if that is present on your system. (CSWbash or SUNWbash are both suitable.)

For Solaris 11 the following packages are required:

  * system/readline
  * system/library/gcc-45-runtime
  * library/security/openssl

These packages are available in the Oracle Solaris release repository (enabled by default on Solaris 11). The PE installer will automatically install them; however, if the release repository is not enabled, the packages will need to be installed manually.

* * *

**Next steps**

* To install Puppet Enterprise on \*nix nodes, continue to [installing Puppet Enterprise](./install_basic.html).
* To install Puppet Enterprise on Windows nodes, continue to [installing Windows agents](./install_windows.html).
* To install Puppet Enterprise on OS X, continue to [installing Mac OS X agents](./install_osx.html).
