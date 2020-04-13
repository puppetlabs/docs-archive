# Configuring and tuning orchestration

After installing PE, you can change some default settings to further configure the orchestrator and pe-orchestration-services.

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

[Tuning standard installations](tuning_standard.md#)

[Configure ulimit for PE services](config_ulimit.md#)

## Configure the orchestrator and pe-orchestration-services

There are several optional parameters you can add to configure the behavior of the orchestrator and pe-orchestration-services. Because they are profile classes, you can change these in the console in the PE Orchestrator group.

### About this task

-   **`puppet_enterprise::profile::orchestrator::task_concurrency`**

    Integer representing the number of simultaneous task or plan actions that can run at the same time in the orchestrator. All task and plan actions are limited by this concurrency limit regardless of transport type \(WinRM, SSH, PCP\).

    Default: `"250"` \(actions\)

-   **`puppet_enterprise::profile::bolt_server::concurrency`**

    An integer that determines the maximum number of simultaneous task or plan requests orchestrator can make to bolt-server. Only task or plan executions on nodes with SSH or WinRM transport methods are limited by this setting because only they require requests to bolt-sever.

    Default: `"100"` \(requests\)

    CAUTION:

    Do not set a concurrency limit that is higher than the bolt-server limit. This can cause timeouts that lead to failed task runs.

-   **`puppet_enterprise::profile::agent::pxp_enabled`**

    Disable or enable the PXP service by setting it to `true` or `false`. If you disable this setting you can’t use the orchestrator or the **Run Puppet** button in the console.

    Default: `true`

-   **`puppet_enterprise::profile::orchestrator::global_concurrent_compiles`**

    An integer that determines how many concurrent compile requests can be outstanding to the master, across all orchestrator jobs.

    Default: `"8"` \(requests\)

-   **`puppet_enterprise::profile::orchestrator::job_prune_threshold`**

    Integer that represents the number of days before job reports are removed.

    Default: `"30"` \(days\)

-   **`puppet_enterprise::profile::orchestrator::pcp_timeout`**

    An integer that represents how many seconds must pass while an agent attempts to connect to a PCP broker. If the agent can’t connect to the broker in that time frame, the run times out.

    Default: `"30"` \(seconds\)

-   **`puppet_enterprise::profile::orchestrator::run_service`**

    Disable or enable orchestration services. Set to `true` or `false`.

    Default: `true`

-   **`puppet_enterprise::profile::orchestrator::use_application_services`**

    Enable or disable application management. Set to `true` or `false`.

    Default: `false`


**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

## Configure the PXP agent

Puppet Execution Protocol \(PXP\) is a messaging system used to request tasks and communicate task statuses. The PXP agent runs the PXP service and you can configure it using Hiera.

### About this task

-   **`puppet_enterprise::pxp_agent::ping_interval`**

    Controls how frequently \(in seconds\) PXP agents will ping PCP brokers. If the brokers don’t respond, the agents try to reconnect.

    Default: `120` \(seconds\)

-   **`puppet_enterprise::pxp_agent::pxp_logfile`**

    A string that represents the path to the PXP agent log file and can be used to debug issues with orchestrator.

    Default:

    -   \*nix: `/var/log/puppetlabs/pxp-agent/pxp-agent.log`

    -   Windows: `C:\Program Data\PuppetLabs\pxp-agent\var\log\pxp-agent.log`

-   **`puppet_enterprise::pxp_agent::spool_dir_purge_ttl`**

    The amount of time to keep records of old Puppet or task runs on agents. You can declare time in minutes \(30m\), hours \(2h\), and days \(14d\).

    Default: `14d`

-   **`puppet_enterprise::pxp_agent::task_cache_dir_purge_ttl`**

    Controls how long tasks are cached after use. You can declare time in minutes \(30m\), hours \(2h\), and days \(14d\).

    Default: `14d`


## Correct ARP table overflow

In larger deployments that use the PCP broker, you might encounter ARP table overflows and need to adjust some system settings.

### About this task

Overflows occur when the ARP table—a local cache of IP address to MAC address resolutions—fills and starts evicting old entries. When frequently used entries are evicted, network traffic will increase to restore them, increasing network latency and CPU load on the broker.

A typical log message looks like:

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


