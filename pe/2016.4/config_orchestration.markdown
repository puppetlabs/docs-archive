---
layout: default
title: "Configuring and tuning orchestration"
canonical: "/pe/latest/config_orchestration.html"
---

We recommend tuning the following settings as needed to improve the performance of the Puppet Communications Protocol (PCP) broker, the Puppet orchestrator, ActiveMQ, or MCollective.


## Disabling and enabling application management

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.
2. On the **Classes** tab, locate or add the `puppet_enterprise::profile::master` class and specify parameters:

   - **Parameter** -- Select **app_management**.

   - **Value** -- Enter `false` to disable the service, or `true` (default) to enable it.

3. Click **Add parameter**, and commit changes.
4. On the Puppet master, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes).

## Disabling and enabling orchestration services

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Orchestrator** group.
2. On the **Classes** tab, locate or add the `puppet_enterprise::profile::orchestrator` class and specify parameters:

   - **Parameter** -- Select **run_service**.

   - **Value** - Enter `false` to disable the service, or `true` (default) to enable it.

3. Click **Add parameter**, and commit changes.
4. On the Puppet master, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes).

## Disabling and enabling the PXP agent

You can optionally disable the PXP service. If you disable this setting you can't use the orchestrator or the **Run Puppet** button in the console.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Agent** group.
2. On the **Classes** tab, locate or add the `puppet_enterprise::profile::agent` class and specify parameters:

   - **Parameter** -- Select **pxp_enabled**.

   - **Value** -- Enter `false` to disable the service, or `true` (default) to enable it.

3. Click **Add parameter**, and commit changes.
4. On the Puppet master, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes).

## Configuring PXP agent log file location

The PXP agent log file can be used to debug issues with the Puppet orchestrator. By default the log files are at  `/var/log/puppetlabs/pxp-agent/pxp-agent.log` (on *nix) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log (on Windows)`. You can configure these locations with Hiera:

1. Add the following parameter to your [Hiera configuration](./config_intro.html#configure-settings-with-hiera):

   ~~~
   puppet_enterprise::pxp_agent::pxp_logfile: '<PATH TO LOG FILE>'
   ~~~

## Tuning orchestrator concurrent compile requests

You can tune how many concurrent compile requests can be outstanding to the Puppet master, across all orchestrator jobs. The default value is eight requests.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Orchestrator** group.
2. On the **Classes** tab, locate the `puppet_enterprise::profile::orchestrator` class and specify parameters:

   - **Parameter** -- Select **global_concurrent_compiles**.

   - **Value** -- Enter the number of concurrent compile requests.

3. Click **Add parameter**, and commit changes.

## Tuning the PCP run timeout

A Puppet agent needs to connect to the PCP broker in order to do Puppet runs via the Puppet orchestrator or the **Run Puppet** button in the console. This parameter allows you to specify how much time should pass before the connection times out.

The Puppet orchestrator defaults to 30 seconds, and the **Run Puppet** button defaults to five seconds. If the agent can't connect to the broker in that time frame, the run will timeout.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Orchestrator** or **PE Console** group.
2. On the **Classes** tab, locate the `puppet_enterprise::profile::orchestrator` or `puppet_enterprise::profile::console_services` class, and specify parameters:

   - **Parameter** Select **pcp_timeout**.

   - **Value** -- Enter the number of seconds that you want to use for the timeout.

3. Click **Add parameter**, and commit changes.
4. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the PE console node. This configures the change and restarts the console services.

## Increasing the ulimit for pe-orchestration-services

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increase the number of allowed file handles.

Instructions for increasing the number of file handles are detailed on the [Increasing the ulimit for PE services](./config_ulimit.html) page.


## Correcting ARP table overflow

In larger PE deployments that use MCollective or the PCP broker, you may encounter ARP table overflows. Overflows occur when the ARP table---a local cache of IP address to MAC address resolutions---fills and starts evicting old entries. When frequently used entries are evicted, this can lead to an increase of extra network traffic (increasing CPU load on the broker and network latency) to restore them.

A typical log message will resemble the following:

~~~
[root@s1 peadmin]# tail -f /var/log/messages
Aug 10 22:42:36 s1 kernel: Neighbour table overflow.
Aug 10 22:42:36 s1 kernel: Neighbour table overflow.
Aug 10 22:42:36 s1 kernel: Neighbour table overflow.
~~~

To workaround this issue, you can increase several [sysctl](https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt) settings related to ARP tables:

~~~
# Set max table size
net.ipv6.neigh.default.gc_thresh3=4096
net.ipv4.neigh.default.gc_thresh3=4096
# Start aggressively clearing the table at this threshold
net.ipv6.neigh.default.gc_thresh2=2048
net.ipv4.neigh.default.gc_thresh2=2048
# Don't clear any entries until this threshold
net.ipv6.neigh.default.gc_thresh1=1024
net.ipv4.neigh.default.gc_thresh1=1024
~~~

These settings are appropriate for networks hosting up to 2000 agents.
