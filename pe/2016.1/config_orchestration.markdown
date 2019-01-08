---
layout: default
title: "Configuring and tuning orchestration"
canonical: "/pe/latest/config_orchestration.html"
---

We recommend tuning the following settings as needed to improve the performance of the Puppet Communications Protocol (PCP) broker, the Puppet orchestrator, ActiveMQ, or MCollective.

## Tuning orchestrator concurrent compile requests

You can tune how many concurrent compile requests can be outstanding to the Puppet master, across all orchestrator jobs. 

The default value is eight requests.

**To change the number of compile requests**:

1. In the PE console, navigate to the **Classification** page (**Nodes** > **Classification**).
2. Click the **PE Orchestrator** group.
3. On the **PE Orchestrator** group page, click the **Classes** tab.
4. Locate the **puppet_enterprise::profile::orchestrator** class, and from the **Parameter** drop-down list, select the **global_concurrent_compiles** parameter.
5. In the Value field, enter the number of concurrent compile requests.
6. Click **Add parameter** and then click the **Commit change** button.

## Tuning the PCP run timeout

A Puppet agent needs to connect to the PCP broker in order to do Puppet runs via the Puppet orchestrator or the **Run Puppet** button in the console. This parameter allows you to specify how much time should pass before the connection times out. 

The Puppet orchestrator defaults to 30 seconds, and the **Run Puppet** button defaults to five seconds. If the agent can't connect to the broker in that time frame, the run will timeout.

**To change the PCP run timeout for the Puppet orchestrator or Run Puppet**:

1. In the PE console, navigate to the **Classification** page (**Nodes** > **Classification**).
2. Click the **PE Orchestrator** group, or click the **PE Console** group.
3. On the **PE Orchestrator** or  **PE Console** group page, click the **Classes** tab.
4. Locate the **puppet_enterprise::profile::orchestrator** or **puppet_enterprise::profile::console_services** class, and from the **Parameter** drop-down list, select the **pcp_timeout** parameter.
5. In the Value field, enter the number of seconds that you want to use for the timeout.
6. Click **Add parameter** and then click the **Commit change** button.
7. Run Puppet on the PE console node. This will configure the change and restart the console services. 


## Tuning ActiveMQ heap usage

The Puppet master runs an ActiveMQ server to route commands. You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on the Puppet master. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Java heap size are detailed on the [configuring Java arguments for PE](./config_java_args.html#pe-puppet-server-service) page.


## Increasing the ulimit for pe-orchestration-services

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

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

## Additional MCollective configuration settings

See [our guide to configuring MCollective](./orchestration_config.html) for additional orchestration config settings.