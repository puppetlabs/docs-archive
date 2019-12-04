# Analytics data collection

Some components automatically collect data about how you use Puppet Enterprise. If you want to opt out of providing this data, you can do so, either during or after installing.

**Related topics**  


[Configuring and tuning your Puppet Enterprise infrastructure](config_intro.md#)

## What data does Puppet Enterprise collect?

Puppet Enterprise \(PE\) collects the following data when Puppet Server starts or restarts, and again every 24 hours.

License, version, master, and agent information:

-   License UUID
-   Number of licensed nodes
-   Product name
-   PE version
-   Master's operating system
-   Master's public IP address
-   Whether the master is running on Microsoft Azure
-   The hypervisor the master is running on, if applicable
-   Number of nodes in deployment
-   Agent operating systems
-   Number of agents running each operating system
-   Agent versions
-   Number of agents running each version of Puppet agent
-   All-in-One \(AIO\) `puppet-agent` package versions
-   Number of agents running on Microsoft Azure or Google Cloud Platform, if applicable

Puppet Enterprise feature use information:

-   Number of node groups in use
-   Number of nodes used in orchestrator jobs since last orchestrator restart
-   Mean nodes per orchestrator job
-   Maximum nodes per orchestrator job
-   Minimum nodes per orchestrator job
-   Total orchestrator jobs created since last orchestrator restart
-   Number of non-default user roles in use
-   Whether MCollective is in use
-   Type of certificate autosigning in use

Puppet Server performance information:

-   Total number of JRuby instances
-   Maximum number of active JRuby instances
-   Maximum number of requests per JRuby instance
-   Average number of instances not in use over the process’s lifetime
-   Average wait time to lock the JRuby pool
-   Average time the JRuby pool held a lock
-   Average time an instance spent handling requests
-   Average time spent waiting to reserve an instance from the JRuby pool
-   Number of requests that timed out while waiting for a JRuby instance
-   Amount of memory the JVM starts with
-   Maximum amount of memory the JVM is allowed to request from the operating system

If PE is installed using an Amazon Web Services Marketplace Image:

-   The marketplace name
-   Marketplace image billing mode \(bring your own license or pay as you go\)

While in use, the console collects the following information:

-   Pageviews
-   Link and button clicks
-   Page load time
-   User language
-   Screen resolution
-   Viewport size
-   Anonymized IP address

The console *does not* collect user inputs such as node or group names, user names, rules, parameters, or variables

The collected data is tied to a unique, anonymized identifier for each master and your site as a whole. No personally identifiable information is collected, and the data we collect is never used or shared outside Puppet, Inc.

### How does sharing this data benefit you?

We use the data to quickly identify organizations that could be affected by a security issue, alert them to the issue, and provide them with steps to take or fixes to download. In addition, the data helps us understand how people use the product, which helps us improve the product to meet your needs.

### How does Puppet use the collected data?

The data we collect is one of many methods we use for learning about our customers. The more we know, the better we can address your needs. For example, knowing how many nodes you manage helps us develop more realistic product testing. And learning which operating systems are the most and the least used helps us decide where to prioritize new functionality. By collecting data, we begin to understand you better as a customer.

## Opt out during the installation process

To opt out of data collection during installation, you must use text-mode installation. Text-mode installation uses the `pe.conf` file to set parameters and values needed for the install.

### About this task

If you prefer to use one of the non-text-mode installation methods, you can opt out of data collection after installation.

### Procedure

1.  Follow the instructions for a monolithic text-mode install or a split text-mode install.

2.  When you edit the `pe.conf` file, set the `puppet_enterprise::send_analytics_data` variable to false:

    `"puppet_enterprise::send_analytics_data": false`

3.  Follow the rest of the text-mode installation instructions.


### Results

By running Puppet with this setting, you will be opted out of data collection.

## Opt out after installing

If you've already installed PE and want to disable data collection, follow these steps.

### Procedure

1.  In the console, click **Classification**, and then click **PE Infrastructure**.

2.  On the **Configuration** tab, on the `puppet_enterprise` class, add `send_analytics_data` as a parameter and set the **Value** to `false`.

3.  On the **Inventory** page, select your master node and click **Run Puppet**.

4.  Select your console node and click **Run Puppet**.


### Results

After Puppet runs to enforce the changes on the master and console nodes, you have opted out of data collection.

