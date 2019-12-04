---
layout: default
title: "Configuring Java arguments for Puppet Enterprise"
canonical: "/pe/latest/config_java_args.html"
---

{:.concept}
You might need to increase the JVM (Java Virtual Machine) memory allocated to Java services or ActiveMQ to improve performance in your Puppet Enterprise deployment.

See [Tuning monolithic installations](./config_monolithic.html) for recommended settings based on various monolithic installation sizes.

{:.task}
## Increase the Java heap size for PE Java services

The Java heap size is the JVM (Java Virtual Machine) memory allocated to Java services in Puppet Enterprise (PE). Make changes to the Java heap size for PE console services, Puppet Server, orchestration services, or PuppetDB in the PE console.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a PE service. The increases shown below are only examples.

1. In the console, click **Classification**. In the **PE Infrastructure** node group, select the appropriate node group.


    | Service             | Node group          | Class                                      |
    |:---------------------|:---------------------|:--------------------------------------------|
    | pe-console-services | **PE Console**      | `puppet_enterprise::profile::console`      |
    | Puppet Server       | **PE Master**       | `puppet_enterprise::profile::master`       |
    | pe-orchestration-services       | **PE Orchestrator** | `puppet_enterprise::profile::orchestrator` |
    | PuppetDB            | **PE PuppetDB**     | `puppet_enterprise::profile::puppetdb`     |
    | ActiveMQ            | **PE ActiveMQ Broker** | `puppet_enterprise::profile::amq::broker` |

2. Click **Classes** and scroll down to the appropriate class.

3. Click the **Parameter name** drop-down list and select `java_args`. Increase the heap size by replacing the parameter with the appropriate JSON string.

    | Service             | Default heap size | New heap size | JSON string                        |
    |:---------------------|:-------------------|:---------------|:------------------------------------|
    | pe-console-services | 256 MB             | 512 MB         | `{"Xmx": "512m", "Xms": "512m"}`   |
    | Puppet Server       | 2 GB               | 4 GB           | `{"Xmx": "4096m", "Xms": "4096m"}` |
    | orchestration-services       | 192 MB             | 1000 MB        | `{"Xmx": "1000m", "Xms": "1000m"}` |
    | PuppetDB            | 256 MB             | 512 MB         | `{"Xmx": "512m", "Xms": "512m"}`   |
    | ActiveMQ   | 512 MB | 1024 MB | `{"Xmx": "1024m", "Xms": "1024m"}`

4. Click **Add Parameter** and then click the commit button.

5. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the appropriate nodes to apply the change. If you're running it on the console node, the console will be unavailable briefly while `pe-console-services` restarts.

    | Service             | Node           |
    |:---------------------|:----------------|
    |  pe-console-services | console        |
    | Puppet Server       | Puppet master and compile masters |
    |pe-orchestration-services       | Puppet master        |
    | PuppetDB            | PuppetDB       |

{:.task}
### ActiveMQ heap usage (Puppet master only)

ActiveMQ uses a default heap size of 512 MB, but you can increase this as needed.

This is the best value for mid-sized deployments, but can be a problem when building small proof-of-concept deployments on memory-starved VMs. This example provides instructions on increasing the heap size to 1024 MB.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by ActiveMQ. The increase shown below is only an example.

**To increase the ActiveMQ heap size**:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE ActiveMQ Broker** group.
2. Click **Classes** and locate the `puppet_enterprise::profile::amq::broker` class.
3. Click the **Parameter name** drop-down list, select `heap_mb`, and in the __value__ field, add a new heap size of 1024.
4. Click **Add Parameter**, and then click the commit button.
5. In the command line on the Puppet maseter node, run `puppet agent -t` to start a Puppet run and apply the change.

{:.task}
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