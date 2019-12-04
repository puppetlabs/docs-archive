---
layout: default
title: "Using load balancers with compile masters"
canonical: "/pe/latest/install_lei_load.html"
---

In an LEI, agent requests are distributed among a pool of compile masters in order to increase the environment's total capacity.

You can configure a simple TCP load balancer to route traffic between agents and compile masters on port 8140.

Specifics on how to configure a load balancer infrastructure falls outside the scope of this document, but examples of how to leverage `haproxy` for this purpose can be found [in the HA proxy module documentation](https://forge.puppetlabs.com/puppetlabs/haproxy).

## Considerations when using load balancers

When load balancing compile masters, there are a number of things to consider to achieve maximum performance.  

### Load balancing for orchestration scale

Puppet communications protocol (PCP) brokers run on compile masters and connect to PXP agents over port 8142.  PCP brokers are built on websockets and require many persistent connections. If you're not [using HTTP health checks](#using-health-checks), we recommend using a round robin or random load balancing algorithm for PXP agent connections to PCP brokers, as PCP brokers don't operate independent of the orchestrator and will isolate themselves if they become disconnected. You can check connections with the [`/status/v1/simple`](./status_api.html#get-statusv1simple) endpoint for an error state. 

You must also configure your load balancer to avoid closing long-lived connections that have little traffic. In the HAproxy module, you can set the `timeout tunnel` to `15m` since PCP brokers will disconnect inactive connections after 15 minutes.

### Using health checks

Puppet's REST API exposes a status endpoint that can be leveraged from a load balancer health check to ensure that unhealthy hosts do not receive agent requests from the load balancer.

The Puppet master service will respond to unauthenticated HTTP GET requests issued to [`/status/v1/simple`](./status_api.html#get-statusv1simple). The API will respond with an HTTP 200 status code if the service is healthy.

If your load balancer doesn't support HTTP health checks, a simpler alternative is to check that the host is listening for TCP connections on port 8140. This ensures that requests will not be forwarded to an unreachable instance of the Puppet master, but it does not guarantee that a host will be pulled out of rotation if it is deemed to be unhealthy, or if the service listening on port 8140 is not a service related to Puppet.

### Optimizing workload distribution

Due to the diverse nature of the network communications between the Puppet agent and the Puppet master, we recommend that you implement a load balancing algorithm that will distribute traffic between compile masters based on the number of open connections. Load balancers often refer to this strategy as "balancing by least connections."

### Configuring `pe_repo` for Puppet agent installation

When installing a new Puppet agent from a load balanced pool of compile masters, the agent configuration will point to whichever compile master handled the request, instead of the load balancer itself. You need to override this behavior to ensure that agents will take advantage of the pooled masters.

See [Step 4 of Installing Compile Masters](./install_multimaster.html#step-4-configure-pe-repo-for-puppet-agent-installation).
