# Configuring Java arguments

You might need to increase the Java Virtual Machine \(JVM\) memory allocated to Java services to improve performance in your Puppet Enterprise \(PE\) deployment.

**Important:** When you enable high availability, you must use Hiera or `pe.conf` only — not the console — to specify configuration parameters. Using `pe.conf` or Hiera ensures that configuration is applied to both your master and replica.

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

[Tuning standard installations](tuning_standard.md#)

## Increase the Java heap size for PE Java services

The Java heap size is the Java Virtual Machine \(JVM\) memory allocated to Java services in Puppet Enterprise \(PE\). You can use any configuration method you choose. We will use the console to change the Java heap size for console services, Puppet Server, orchestration services, or PuppetDB in the examples below.

### About this task

**Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a service. The increases shown below are only examples.

### Procedure

1.  In the console, click **Classification**. In the **PE Infrastructure** node group, select the node group that corresponds to the service you're increasing heap size for.

    |Service|Node group|
    |:------|:---------|
    |pe-console-services|PE Console|
    |Puppet Server|PE Master|
    |pe-orchestration-services|PE Orchestrator|
    |PuppetDB|PE PuppetDB|
    |ActiveMQ|PE ActiveMQ Broker|

2.  Click **Configuration** and scroll down to the appropriate class.

    |Node group|Class|
    |:---------|:----|
    |PE Console|`puppet_enterprise::profile::console`|
    |PE Master|`puppet_enterprise::profile::master`|
    |PE Orchestrator|`puppet_enterprise::profile::orchestrator`|
    |PE PuppetDB|`puppet_enterprise::profile::puppetdb`|
    |PE ActiveMQ Broker|`puppet_enterprise::profile::amq::broker`|

3.  Click the **Parameter name** list and select `java_args`. Increase the heap size by replacing the parameter with the appropriate JSON string.

    |Service|Default heap size|New heap size|JSON string|
    |:------|:----------------|:------------|:----------|
    |pe-console-services|256 MB|512 MB|`{"Xmx": "512m", "Xms": "512m"}`|
    |Puppet Server|2 GB|4 GB|`{"Xmx": "4096m", "Xms": "4096m"}`|
    |orchestration-services|192 MB|1000 MB|`{"Xmx": "1000m", "Xms": "1000m"}`|
    |PuppetDB|256 MB|512 MB|`{"Xmx": "512m", "Xms": "512m"}`|
    |ActiveMQ|512 MB|1024 MB|`{"Xmx": "1024m", "Xms": "1024m"}`|

4.  Click **Add Parameter** and then commit changes.

5.  Run Puppet on the appropriate nodes to apply the change. If you're running it on the console node, the console will be unavailable briefly while `pe-console-services` restarts.

    |Service|Node|
    |:------|:---|
    |pe-console-services|console|
    |Puppet Server|master and compile masters|
    |pe-orchestration-services|master|
    |PuppetDB|PuppetDB|


**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

## Disable Java garbage collection logging

Java garbage collection logs can be useful when diagnosing performance issues with JVM-based PE services. Garbage collection logs are enabled by default, and the results are captured in the support script, but you can disable them.

### Procedure

1.  Use Hiera or pe.conf to disable garbage collection logging by adding or changing the following parameters, as needed:

    ```
    puppet_enterprise::console_services::enable_gc_logging: false
    puppet_enterprise::master::puppetserver::enable_gc_logging: false
    puppet_enterprise::profile::orchestrator::enable_gc_logging: false
    puppet_enterprise::puppetdb::enable_gc_logging: false
    puppet_enterprise::profile::amq::broker::enable_gc_logging: false
    ```


