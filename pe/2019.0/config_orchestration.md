# Configuring and tuning orchestration

After installing PE, you can change some default settings to further configure the orchestrator and pe-orchestration-services.

**Related information**  


[Tuning monolithic installations](tuning_monolithic.md#)

[Configure settings with Hiera](config_intro.md#)

[Configure ulimit for PE services](config_ulimit.md#)

## Configure the orchestrator and pe-orchestration-services

There are several parameters you can add to configure the behavior of the orchestrator and pe-orchestration-services.

### About this task



### Procedure

1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Orchestrator** group.

2.  On the **Configuration** tab, locate the `puppet_enterprise` class indicated and add any of the following parameters and values as needed.

    |Parameter|Value|
    |---------|-----|
    |    ```
puppet_enterprise::profile::agent::pxp_enabled
    ```

|Disable or enable the PXP service. Set to `true` or `false`. If you disable this setting you can’t use the orchestrator or the **Run Puppet** button in the console. Enabled \(`true`\) by default.|
    |    ```
puppet_enterprise::profile::bolt_server::concurrency
    ```

|An integer that determines the maximum number of concurrent requests orchestrator can make to bolt-server. The default value is set to the current value stored for bolt-server. CAUTION:

Do not set a concurrency limit that is higher than the bolt-server limit. This can cause timeouts that lead to failed task runs.

|
    |    ```
puppet_enterprise::profile::orchestrator::global_concurrent_compiles
    ```

|An integer that determines how many concurrent compile requests can be outstanding to the master, across all orchestrator jobs. The default value is `"8"`.|
    |    ```
puppet_enterprise::profile::orchestrator::job_prune_threshold
    ```

|Integer representing the days after which job reports should be removed. Defaults to`"30"` days|
    |    ```
puppet_enterprise::profile::orchestrator::pcp_timeout
    ```

|An agent needs to connect to the PCP broker in order to do Puppet runs via the orchestrator. Set an integer to specify how much time should pass before the connection times out.The orchestrator defaults to `"30"` seconds. If the agent can’t connect to the broker in that time frame, the run times out.|
    |    ```
puppet_enterprise::profile::orchestrator::run_service
    ```

|Disable or enable orchestration services. Set to `true` or `false`. Enabled \(`true`\) by default.|
    |    ```
puppet_enterprise::profile::orchestrator::task_concurrency
    ```

|Integer representing the number of tasks that can run at the same time. Defaults to `"250"` tasks.|
    |    ```
puppet_enterprise::profile::orchestrator::use_application_services
    ```

|Disable or enable application management. Set to `true` or `false`. Disabled \(`false`\) by default.|
    |    ```
puppet_enterprise::pxp_agent::ping_interval
    ```

|Controls how frequently PXP agents ping PCP brokers. If the agents don't receive responses, they attempt to reconnect. Defaults to `"120"` seconds.|
    |    ```
puppet_enterprise::pxp_agent::pxp_logfile
    ```

|A string that represents the path to the PXP agent log file. Change as needed. By default, the log files are located at:     -   \*nix: `/var/log/puppetlabs/pxp-agent/pxp-agent.log`

    -   Windows: `C:\Program Data\PuppetLabs\pxp-agent\var\log\pxp-agent.log`

|
    |    ```
puppet_enterprise::pxp_agent::spool_dir_purge_ttl
    ```

|The amount of time to keep records of old Puppet or task runs on agents. You can declare time in minutes \(30m\), hours \(2h\), and days \(14d\).|
    |    ```
puppet_enterprise::pxp_agent::task_cache_dir_purge_ttl
    ```

|Controls how long tasks should be cached after use. By default, unused tasks are purged after 2 weeks. You can declare time in minutes \(30m\), hours \(2h\), and days \(14d\).|

3.  Click **Add Parameter** as needed, and commit changes.

4.  On the node hosting the master, run Puppet.


**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

## Configure PXP agent log file location

Use the PXP agent log file to debug issues with the Puppet orchestrator.

### About this task

By default the log files are at `/var/log/puppetlabs/pxp-agent/pxp-agent.log` \(on \*nix\) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log` \(on Windows\). You can configure these locations with Hiera.

### Procedure

1.  Add the following parameter to your Hiera configuration:

    ```
    puppet_enterprise::pxp_agent::pxp_logfile: '<PATH TO LOG FILE>'
    ```


## Correct ARP table overflow

In larger deployments that use the PCP broker, you may encounter ARP table overflows and need to adjust some system settings.

### About this task

Overflows occur when the ARP table—a local cache of IP address to MAC address resolutions—fills and starts evicting old entries. When frequently used entries are evicted, this can lead to an increase of extra network traffic \(increasing CPU load on the broker and network latency\) to restore them.

A typical log message resembles the following:

```
[root@s1 peadmin]# tail -f /var/log/messages
Aug 10 22:42:36 s1 kernel: Neighbour table overflow.
Aug 10 22:42:36 s1 kernel: Neighbour table overflow.
Aug 10 22:42:36 s1 kernel: Neighbour table overflow.
```

To work around this issue:

### Procedure

1.  Increase sysctl settings related to ARP tables.

    For example, the following settings are appropriate for networks hosting up to 2000 agents:

    ```
    # Set max table size
    net.ipv6.neigh.default.gc_thresh3=4096
    net.ipv4.neigh.default.gc_thresh3=4096
    # Start aggressively clearing the table at this threshold
    net.ipv6.neigh.default.gc_thresh2=2048
    net.ipv4.neigh.default.gc_thresh2=2048
    # Don't clear any entries until this threshold
    net.ipv6.neigh.default.gc_thresh1=1024
    net.ipv4.neigh.default.gc_thresh1=1024
    ```


