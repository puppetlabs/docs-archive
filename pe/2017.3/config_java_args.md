# Configuring Java arguments for Puppet Enterprise

You might need to increase the Java Virtual Machine \(JVM\) memory allocated to Java services or ActiveMQ to improve performance in your Puppet Enterprise \(PE\) deployment.

**Related topics**  


[Tuning monolithic installations](tuning_monolithic.md#)

[Configure settings with Hiera](config_intro.md#)

## Increase the Java heap size for PE Java services

The Java heap size is the Java Virtual Machine \(JVM\) memory allocated to Java services in Puppet Enterprise \(PE\). Use the console to change the Java heap size for console services, Puppet Server, orchestration services, or PuppetDB.

### About this task

**Note:** Ensure that you have sufficient free memory before increasing the memory that is used by a service. The increases shown below are only examples.

### Procedure

1.  In the console, click **Classification**. In the **PE Infrastructure** node group, select the appropriate node group.

    |Service|Node group|Class|
    |:------|:---------|:----|
    |pe-console-services|PE Console|`puppet_enterprise::profile::console`|
    |Puppet Server|PE Master|`puppet_enterprise::profile::master`|
    |pe-orchestration-services|PE Orchestrator|`puppet_enterprise::profile::orchestrator`|
    |PuppetDB|PE PuppetDB|`puppet_enterprise::profile::puppetdb`|
    |ActiveMQ|PE ActiveMQ Broker|`puppet_enterprise::profile::amq::broker`|

2.  Click **Configuration** and scroll down to the appropriate class.

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


**Related topics**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

## Increase ActiveMQ heap usage \(master only\)

ActiveMQ uses a default heap size of 512MB, but you can increase this as needed.

### About this task

512MB is the best value for mid-sized deployments, but can be a problem when building small proof-of-concept deployments on memory-starved VMs.

**Note:** Ensure that you have sufficient free memory before increasing the memory that is used by ActiveMQ. The increase shown below is only an example.

### Procedure

1.  In the console, click **Nodes** \> **Classification**, and in the **PE Infrastructure** node group, select the **PE ActiveMQ Broker** group. 

2.  Click **Configuration** and locate the `puppet_enterprise::profile::amq::broker` class.

3.  Click the **Parameter name** list, select `heap_mb`, and in the **value** field, enter 1024.

4.  Click **Add Parameter**, and commit changes.

5.  On the command line on the master, run `puppet agent -t` to start a Puppet run and apply the change.


## Disable Java garbage collection logging

Java garbage collection logs can be useful when diagnosing performance issues with JVM-based PE services. Garbage collection logs are enabled by default, and the results are captured in the support script, but you can disable them.

### Procedure

1.  Disable garbage collection logging by editing your Hiera default `.yaml` file. Add any of the following parameters as needed:

    ```
    puppet_enterprise::console_services::enable_gc_logging: false
    puppet_enterprise::master::puppetserver::enable_gc_logging: false
    puppet_enterprise::profile::orchestrator::enable_gc_logging: false
    puppet_enterprise::puppetdb::enable_gc_logging: false
    puppet_enterprise::profile::amq::broker::enable_gc_logging: false
    ```


