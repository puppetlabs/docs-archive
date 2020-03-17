# Analytics data collection

Some components automatically collect data about how you use Puppet Enterprise. If you want to opt out of providing this data, you can do so, either during or after installing.

**Parent topic:**[Configuring Puppet Enterprise](configuring_pe.md)

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

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
-   Number of configured high availability replicas, if applicable

Puppet Enterprise feature use information:

-   Number of node groups in use
-   Number of nodes used in orchestrator jobs after last orchestrator restart
-   Mean nodes per orchestrator job
-   Maximum nodes per orchestrator job
-   Minimum nodes per orchestrator job
-   Total orchestrator jobs created after last orchestrator restart
-   Number of non-default user roles in use
-   Type of certificate autosigning in use
-   Number of nodes in the job that were run over Puppet Communications Protocol
-   Number of nodes in the job that were run over SSH.
-   Number of nodes in the job that were run over WinRM.
-   List of Puppet task jobs
-   List of Puppet deploy jobs
-   List of Puppet task jobs run by plans
-   List of file upload jobs run by plans
-   List of script jobs run by plans
-   List of command jobs run by plans
-   List of wait jobs run by plans
-   How nodes were selected for the job
-   Whether the job was started by the PE admin account
-   Number of nodes in the job
-   Length of description applied to the job
-   Length of time the job ran
-   User agent used to start the job \(to distinguish between the console, command line, and API\)
-   UUID used to correlate multiple jobs run by the same user
-   Time the task job was run
-   How nodes were selected for the job
-   Whether the job was started by the PE admin account
-   Number of nodes in the job
-   Length of description applied to the job
-   Whether the job was asked to override agent-configured no-operation \(no-op\) mode
-   Whether app-management was enabled in the orchestrator for this job
-   Time the deploy job was run
-   Type of version control system webhook
-   Whether the request was to deploy all environments
-   Whether code-manager will wait for all deploys to finish or error to return a response
-   Whether the deploy is a dry-run
-   List of environments requested to deploy
-   List of deploy requests
-   Total time elapsed for all deploys to finish or error
-   List of total wait times for deploys specifying `--wait` option
-   Name of environment deployed
-   Time needed for r10k to run
-   Time spent committing to file sync
-   Time elapsed for all environment hooks to run
-   List of individual environment deploys
-   Puppet classes applied from publicly available modules, with node counts per class


Backup and Restore information:

-   Whether user used `--force` option when running restore
-   Scope of restore
-   Time in seconds for various restore functions
-   Time to check for disk space to restore
-   Time to stop PE related services
-   Time to restore PE file system components
-   Time to migrate PE configuration for new server
-   Time to configure PE on newly restored master
-   Time to update PE classification for new server
-   Time to deactivate the old master node
-   Time to restore the pe-orchestrator database
-   Time to restore the pe-rbac database
-   Time to restore the pe-classifier database
-   Time to restore the pe-activity database
-   Time to restore the pe-puppetdb database
-   Total time to restore
-   List of puppet backup restore jobs
-   Whether user used `--force` option when running `puppet-backup create`
-   Whether user used `--dir` option when running `puppet-backup create`
-   Whether user used `--name` option when running `puppet-backup create`
-   Scope of backup
-   Time in seconds for various back up functions
-   Time needed to estimate backup size, disk space needed, and disk space available
-   Time to create file system backup
-   Time to back up the pe-orchestrator database
-   Time to back up the pe-rbac database
-   Time to back up the pe-classifier database
-   Time to back up the pe-activity database
-   Time to back up the pe-puppetdb database
-   Time to compress archive file to backup directory
-   Time to back up PE related classification
-   Total time to back up
-   List of `puppet-backup create` jobs

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

Installer information:

-   Installation method \(express, text, web, repair\)
-   Current version, if upgrading
-   Target version
-   Success or failure, and limited information on the type of failure, if an

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

We use the data to identify organizations that could be affected by a security issue, alert them to the issue, and provide them with steps to take or fixes to download. In addition, the data helps us understand how people use the product, which helps us improve the product to meet your needs.

### How does Puppet use the collected data?

The data we collect is one of many methods we use for learning about our customers. For example, knowing how many nodes you manage helps us develop more realistic product testing. And learning which operating systems are the most and the least used helps us decide where to prioritize new functionality. By collecting data, we begin to understand you as a customer.

## Opt out during the installation process

To opt out of data collection during installation, you can set the `DISABLE_ANALYTICS` environment variable when you run the installer script. This opt-out method works for all installation methods: express, text, and web-based.

Setting the `DISABLE_ANALYTICS` environment variable during installation sets `puppet_enterprise::send_analytics_data: false` in `pe.conf`, opting you out of data collection.

1.  Follow the instructions for your chosen installation method, adding `DISABLE_ANALYTICS=1` when you call the installer script, for example:

    ```
    sudo DISABLE_ANALYTICS=1 ./puppet-enterprise-installer
    ```


## Opt out after installing

If you've already installed PE and want to disable data collection, follow these steps.

1.  In the console, click **Classification**, and then click **PE Infrastructure**.

2.  On the **Configuration** tab, on the `puppet_enterprise::profile::master` class, add `send_analytics_data` as a parameter and set the **Value** to `false`.

3.  On the **Inventory** page, select your master node and click **Run Puppet**.

4.  Select your console node and click **Run Puppet**.


After Puppet runs to enforce the changes on the master and console nodes, you have opted out of data collection.

