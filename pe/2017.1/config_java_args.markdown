---
layout: default
title: "Configuring Java arguments for Puppet Enterprise"
canonical: "/pe/latest/config_java_args.html"
---

You might need to increase the JVM (Java Virtual Machine) memory allocated to Java services or ActiveMQ to improve performance in your Puppet Enterprise deployment.

See [Tuning monolithic installations](./config_monolithic.html) for recommended settings based on various monolithic installation sizes.

## Increase the Java heap size for PE Java services

The Java heap size is the JVM (Java Virtual Machine) memory allocated to Java services in Puppet Enterprise (PE). Make changes to the Java heap size for PE console services, Puppet Server, orchestration services, or PuppetDB in the PE console.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a PE service. The increases shown below are only examples.

1. In the console, click **Nodes** > **Classification**. In the **PE Infrastructure** node group, select the appropriate node group.


    | Service             | Node group          | Class                                      |
    |:---------------------|:---------------------|:--------------------------------------------|
    | pe-console-services | **PE Console**      | `puppet_enterprise::profile::console`      |
    | Puppet Server       | **PE Master**       | `puppet_enterprise::profile::master`       |
    | pe-orchestration-services       | **PE Orchestrator** | `puppet_enterprise::profile::orchestrator` |
    | PuppetDB            | **PE PuppetDB**     | `puppet_enterprise::profile::puppetdb`     |

2. Click **Classes** and scroll down to the appropriate class.

3. Click the **Parameter name** drop-down list and select `java_args`. Increase the heap size by replacing the parameter with the appropriate JSON string.

    | Service             | Default heap size | New heap size | JSON string                        |
    |:---------------------|:-------------------|:---------------|:------------------------------------|
    | pe-console-services | 256MB             | 512MB         | `{"Xmx": "512m", "Xms": "512m"}`   |
    | Puppet Server       | 2GB               | 4GB           | `{"Xmx": "4096m", "Xms": "4096m"}` |
    | orchestration-services       | 192MB             | 1000MB        | `{"Xmx": "1000m", "Xms": "1000m"}` |
    | PuppetDB            | 256MB             | 512MB         | `{"Xmx": "512m", "Xms": "512m"}`   |

4. Click **Add Parameter** and then click the commit button.

5. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the appropriate nodes to apply the change. If you're running it on the console node, the console will be unavailable briefly while `pe-console-services` restarts.

    | Service             | Node           |
    |:---------------------|:----------------|
    |  pe-console-services | console        |
    | Puppet Server       | Puppet master and compile masters |
    |pe-orchestration-services       | Puppet master        |
    | PuppetDB            | PuppetDB       |

### ActiveMQ heap usage (Puppet master only)

The Puppet master runs an ActiveMQ server to route commands. By default, its process uses 512MB, the best Java heap size for mid-sized deployments. Avoid problems when building small proof-of-concept deployments on memory-starved VMs by increasing the heap size.

> **Note:** Ensure that you have sufficient free memory before increasing the memory that is used by ActiveMQ. The increase shown below is only an example.

1. In the console, click **Nodes** > **Classification**. In the **PE Infrastructure** node group, select the **PE ActiveMQ Broker** group.

2. Click **Classes** and locate the `puppet_enterprise::profile::amq::broker` class.

3. Click the **Parameter name** drop-down list, select `heap_mb`, and in the __value__ field, add a new heap size of 1024.

4. Click **Add Parameter**, and then click the commit button.

5. In the command line on the Puppet master node, run `puppet agent -t` to start a Puppet run and apply the change.

To return the setting to default, complete steps one - five changing the value back to the original setting in step three.