# Configuring Java arguments

You might need to increase the Java Virtual Machine \(JVM\) memory allocated to Java services to improve performance in your Puppet Enterprise \(PE\) deployment.

**Important:** When you enable high availability, you must use Hiera or `pe.conf` only — not the console — to specify configuration parameters. Using `pe.conf` or Hiera ensures that configuration is applied to both your master and replica.

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

[Tuning monolithic installations](tuning_monolithic.md#)

## Increase the Java heap size for PE Java services

The Java heap size is the Java Virtual Machine \(JVM\) memory allocated to Java services in Puppet Enterprise \(PE\). Because these are `profile` classes, you can use the console to change the Java heap size for console services, Puppet Server, orchestration services, or PuppetDB.

### Before you begin

Ensure that you have sufficient free memory before increasing the memory that is used by a service.

### About this task

**Tip:** To configure `java_args` for Razor, you must hard code them in the `init` script. 

### Procedure

1.  In the console, click **Classification**. In the **PE Infrastructure** node group, select the appropriate node group.

    |Service|Node group|Class|
    |:------|:---------|:----|
    |pe-console-services|PE Console|`puppet_enterprise::profile::console`|
    |Puppet Server|PE Master|`puppet_enterprise::profile::master`|
    |pe-orchestration-services|PE Orchestrator|`puppet_enterprise::profile::orchestrator`|
    |PuppetDB|PE PuppetDB|`puppet_enterprise::profile::puppetdb`|

2.  Click **Configuration** and scroll down to the appropriate class.

3.  Click the **Parameter name** list and select `java_args`. Increase the heap size by replacing the parameter with the appropriate JSON string.

    **Note:** The increases shown here are examples.

    |Service|Default heap size|New heap size|JSON string|
    |:------|:----------------|:------------|:----------|
    |pe-console-services|256 MB|768 MB|`{"Xmx": "768m", "Xms": "768m"}`|
    |Puppet Server|2048 MB|3840 MB|`{"Xmx": "3840m", "Xms": "3840m"}`|
    |orchestration-services|192 MB|768 MB|`{"Xmx": "768m", "Xms": "768m"}`|
    |PuppetDB|256 MB|1024 MB|`{"Xmx": "1024m", "Xms": "1024m"}`|

4.  Click **Add Parameter** and then commit changes.

5.  Run Puppet on the appropriate nodes to apply the change. If you're running it on the console node, the console is unavailable briefly while `pe-console-services` restarts.

    |Service|Node|
    |:------|:---|
    |pe-console-services|console|
    |Puppet Server|master and compilers|
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


