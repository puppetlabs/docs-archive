---
layout: default
title: "Configuring Java arguments For Puppet Enterprise"
canonical: "/pe/latest/config_java_args.html"
---

See [Tuning monolithic installations](./config_monolithic.html) for recommended settings based on various monolithic installation sizes.

## Increasing the Java heap size for PE Java services

This section provides instructions for increasing the JVM (Java Virtual Machine) memory that is allocated to Java services in Puppet Enterprise (PE). This memory allocation is known as the Java heap size. It can be adjusted through the PE console as described in the following procedures.


### PE console services

PE console-services uses a default heap size of 256 MB, but you can increase this as needed.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a PE service. The increase shown below is only an example.

**To increase the Java heap size for `pe-console-services`**:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE Console** node group.
2. Click **Classes** and scroll down to the `puppet_enterprise::profile::console` class.
3. Click the **Parameter name** drop-down list and select `java_args`. Replace the parameter value with the JSON string `{"Xmx": "512m", "Xms": "512m"}`. This increases the heap size from the default of 256 MB to 512 MB.
4. Click **Add Parameter**, and then click the commit button.
5. In the command line on the console node, run `puppet agent -t` to start a Puppet run and apply the change. The console will be unavailable briefly while `pe-console-services` restarts.

### PE Puppet Server service

The Puppet server uses a default heap size of 2 GB, but you can increase this as needed.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a PE service. The increase shown below is only an example.

**To increase the Java heap size for `pe-puppetserver`**:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE Master** node group.
2. Click **Classes** and scroll down to the `puppet_enterprise::profile::master` class.
3. Click the **Parameter name** drop-down list and select `java_args`. Replace the parameter value with the JSON string `{"Xmx": "4096m", "Xms": "4096m"}`. This increases the heap size from the default of 2 GB to 4 GB.
4. In the command line on each compile master, run `puppet agent -t` to start a Puppet run and apply the change.

### PE orchestration services

PE orchestration-services uses a default heap size of 500 MB, but you can increase this as needed.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a PE service. The increase shown below is only an example.

**To increase the Java heap size for `pe-orchestration-services`**:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE Orchestrator** node group.
2. Click **Classes** and scroll down to the `puppet_enterprise::profile::orchestrator` class.
3. Click the **Parameter name** drop-down list and select `java_args`. Replace the parameter value with the JSON string `{"Xmx": "1000m", "Xms": "1000m"}`. 
4. Click **Add Parameter**, and then click the commit button.
5. In the command line on the Puppet master, run `puppet agent -t` to start a Puppet run and apply the change.


### PuppetDB

PuppetDB uses a default heap size of 256 MB, but you can increase this as needed.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a PuppetDB. The increase shown below is only an example.

**To increase the Java heap size for PuppetDB**:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE PuppetDB** node group.
2. Click **Classes** and scroll down to the `puppet_enterprise::profile::puppetdb` class.
3. Click the **Parameter name** drop-down list and select `java_args`. Replace the parameter value with the JSON string `{"Xmx": "512m", "Xms": "512m"}`. This increases the heap size from the default of 256 MB to 512 MB.
4. Click **Add Parameter**, and then click the commit button.
5. In the command line on the PuppetDB node, run `puppet agent -t` to start a Puppet run and apply the change.

### ActiveMQ heap usage (Puppet master only)

The Puppet master runs an ActiveMQ server to route commands. By default, its process uses a Java heap size of 512 MB. 

This is the best value for mid-sized deployments, but can be a problem when building small proof-of-concept deployments on memory-starved VMs. This example provides instructions on increasing the heap size to 1024 MB.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by ActiveMQ. The increase shown below is only an example.

**To increase the ActiveMQ heap size**:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE ActiveMQ Broker** group.
2. Click **Classes** and locate the `puppet_enterprise::profile::amq::broker` class.
3. Click the **Parameter name** drop-down list, select `heap_mb`, and in the __value__ field, add a new heap size of 1024.
4. Click **Add Parameter**, and then click the commit button.
5. In the command line on the Puppet maseter node, run `puppet agent -t` to start a Puppet run and apply the change.


You can later delete the variable to revert to the default setting.

## Disable Java garbage collection logging

Java garbage collection (GC) logs can be useful when diagnosing performance issues with JVM-based PE services. GC logs are enabled by default in PE, and the results are captured in the support script, but you can disable them if you need to.

1. Disable GC logging by [editing your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera). Add any of the following parameters as needed. 

   ~~~
   puppet_enterprise::console_services::enable_gc_logging: false 
   puppet_enterprise::master::puppetserver::enable_gc_logging: false 
   puppet_enterprise::profile::orchestrator::enable_gc_logging: false 
   puppet_enterprise::puppetdb::enable_gc_logging: false 
   puppet_enterprise::profile::amq::broker::enable_gc_logging: false 
   ~~~
