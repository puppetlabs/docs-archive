---
layout: default
title: "Upgrading large environment installations"
canonical: "/pe/latest/install_multimaster_upgrade.html"
---

Upgrading your large environment installation (LEI) involves a combination of steps that you must perform across your core Puppet Enterprise (PE) components, your compile masters, and your ActiveMQ hubs and spokes.

This doc details the steps you’ll need to take to upgrade your LEI.

>#### A note about load balancers
>
>In this procedure MASTER.EXAMPLE.COM refers to the Puppet Master/CA server, also known as the master of masters (MoM).
>
>However, in some cases, you may be using a load balancer in your LEI. If this is the case, you’ll need to point the agent install script at the correct machines when you upgrade agents on the compile masters, ActiveMQ hubs and spokes, and Puppet agent nodes, as detailed in [the load balancer documentation](./install_multimaster.html#using-load-balancers-in-a-large-environment-installation). If you followed those steps, the compile masters will point their upgrade scripts at MASTER.EXAMPLE.COM (or the MoM), and the remaining agents will point at LOADBALANCER.EXAMPLE.COM (which has inherited the `pe_repo` class from the PE Master group). The exact instructions will vary depending on your setup.

**To upgrade your large environment installation**:

### Step 1: Upgrade PE

Follow the [split upgrade instructions](./upgrade_split.html) to upgrade the core PE components (the main Puppet master, PuppetDB, and the PE console).

### Step 2: Upgrade each Puppet compile master

SSH into each Puppet compile master, and run the following command:
  
~~~
/opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem    https://<MASTER.EXAMPLE.COM>:8140/packages/current/upgrade.bash | sudo bash
~~~

### Step 3: Upgrade each ActiveMQ hub

SSH into each ActiveMQ hub, and run the following command:

~~~
/opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER.EXAMPLE.COM>:8140/packages/current/upgrade.bash | sudo bash
~~~

### Step 4: Upgrade each ActiveMQ spoke

SSH into each ActiveMQ spoke, and run the following command:

~~~
/opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER.EXAMPLE.COM>:8140/packages/current/upgrade.bash | sudo bash
~~~

### Step 5: Upgrade Puppet agent nodes

Follow the [agent upgrade documentation](./install_upgrading_agents.html) to upgrade the Puppet agent nodes in your LEI.

[apt]: https://forge.puppetlabs.com/puppetlabs/apt
[inifile]: https://forge.puppetlabs.com/puppetlabs/inifile
[stdlib]: https://forge.puppetlabs.com/puppetlabs/stdlib
[puppet_agent]: https://forge.puppetlabs.com/puppetlabs/puppet_agent

> **Important**: Before upgrading agents, you must install the [puppetlabs-puppet_agent][puppet_agent] upgrade module on each compile master.
>
>If you are upgrading agents in a PE infrastructure without internet access, you must install the [puppetlabs-puppet_agent][puppet_agent] upgrade module, the [puppetlabs-inifile][inifile] module, the [puppetlabs-stdlib][stdlib] module, and the [puppetlabs-apt][apt] module on each compile master.
