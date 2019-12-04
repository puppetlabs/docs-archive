---
layout: default
title: "Configuring and tuning orchestration"
canonical: "/pe/latest/config_orchestration.html"
---

After installing PE, you can change some default settings to further configure the orchestrator and pe-orchestration-services.

## Configure the orchestrator and pe-orchestration-services

There are several parameters you can add to configure the behavior of the orchestrator and pe-orchestration-services.

1. In the PE console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Orchestrator** group.  
2. On the **Classes** tab, locate the `puppet_enterprise` class indicated and add any of the following parameters and values as needed.

   <table>
     <tr>
       <th>Parameter</th>
       <th>Value</th>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::master::app_management</code></td>
       <td nowrap>Disable or enable application management. Set to <code>true</code> or <code>false</code>.<br>Enabled (<code>true</code>) by default.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::orchestrator::run_service</code></td>
       <td>Disable or enable orchestration services. Set to <code>true</code> or <code>false</code>.<br>Enabled (<code>true</code>) by default.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::agent::pxp_enabled</code></td>
       <td>Disable or enable the PXP service. Set to <code>true</code> or <code>false</code>.<br>If you disable this setting you can’t use the orchestrator or the <b>Run Puppet</b> button in the console. <br>Enabled (<code>true</code>) by default.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::orchestrator::global_concurrent_compiles</code></td>
       <td>An integer that determines how many concurrent compile requests can be outstanding to the Puppet master, across all orchestrator jobs. <br>The default value is <code>"8"</code>.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::orchestrator::pcp_timeout</code></td>
       <td>A Puppet agent needs to connect to the PCP broker in order to do Puppet runs via the Puppet orchestrator. Set an integer to specify how much time should pass before the connection times out.<br> The Puppet orchestrator defaults to <code>"30"</code> seconds. If the agent can’t connect to the broker in that time frame, the run will timeout.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::pxp_agent::pxp_logfile</code></td>
       <td>A string that represents the path to the PXP agent log file. Change as needed. By default, the log files are at  <code>/var/log/puppetlabs/pxp-agent/pxp-agent.log</code> (on *nix) or <code>C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log</code> (on Windows).</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::pxp_agent::ping_interval</code></td>
       <td>Controls how frequently PXP agents will ping PCP brokers. If the agents don't receive responses, they will attempt to reconnect.<br> Defaults to <code>"120"</code> seconds.</td>
     </tr>
   </table>
   
3. Click **Add parameter** as needed, and commit changes.
4. On the node hosting the Puppet master, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes).  

## Configure PXP agent log file location

The PXP agent log file can be used to debug issues with the Puppet orchestrator. By default the log files are at  `/var/log/puppetlabs/pxp-agent/pxp-agent.log` (on *nix) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log (on Windows)`. You can configure these locations with Hiera:

1. Add the following parameter to your [Hiera configuration](./config_intro.html#configure-settings-with-hiera):

   ~~~
   puppet_enterprise::pxp_agent::pxp_logfile: '<PATH TO LOG FILE>'
   ~~~

## Correct ARP table overflow

In larger PE deployments that use MCollective or the PCP broker, you may encounter ARP table overflows and need to adjust some system settings. 

Overflows occur when the ARP table---a local cache of IP address to MAC address resolutions---fills and starts evicting old entries. When frequently used entries are evicted, this can lead to an increase of extra network traffic (increasing CPU load on the broker and network latency) to restore them.

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

***
**Related links**

- Increasing the ulimit for pe-orchestration-services

   The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

   Instructions for increasing the number of file handles are detailed on the [Increasing the ulimit for PE services](./config_ulimit.html) page.
