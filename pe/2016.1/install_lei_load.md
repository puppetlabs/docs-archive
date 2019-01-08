---
layout: default
title: "Using load balancers in an LEI"
canonical: "/pe/latest/install_lei_load.html"
---

In an LEI, agent requests are distributed among a pool of compile masters in order to increase the environment's total capacity.
You can configure a simple TCP load balancer to route traffic between agents and compile masters on port 8140.

Specifics on how to configure a load balancer infrastructure falls outside the scope of this document, but examples of how to leverage `haproxy` for this purpose can be found [in the HA proxy module documentation](https://forge.puppetlabs.com/puppetlabs/haproxy).

## Considerations when using load balancers

### Using health checks

Puppet's REST API exposes a status endpoint that can be leveraged from a load balancer health check to ensure that unhealthy hosts do not receive agent requests from the load balancer.

The Puppet master service will respond to unauthenticated HTTP GET requests issued to `/puppet/v3/status/:name?environment=:environment` where `:name` is set to any alphanumeric value, and `:environment` is set to the name of any Puppet environment present on the host. A response with an HTTP 200 status code will be returned if the service is healthy.

If your load balancer doesn't support HTTP health checks, a simpler alternative is to check that the host is listening for TCP connections on port 8140. This ensures that requests will not be forwarded to an unreachable instance of the Puppet master, but it does not guarantee that a host will be pulled out of rotation if it is deemed to be unhealthy, or if the service listening on port 8140 is not a service related to Puppet.

### Optimizing workload distribution

Due to the diverse nature of the network communications between the Puppet agent and the Puppet master, we recommend that you implement a load balancing algorithm that will distribute traffic between compile masters based on the number of open connections. Load balancers often refer to this strategy as "balancing by least connections."

## Installing Puppet agents with a load balancer

### Configuring `pe_repo` for Puppet agent installation

When installing a new Puppet agent from a load balanced pool of compile masters, the agent configuration will point to whichever compile master handled the request, instead of the load balancer itself. You need to override this behavior to ensure that agents will take advantage of the pooled masters.

**To point agent installs at the load balancer**:

1. From the console, click __Nodes__ > __Classification__, and select the __PE Master__ group.
2. In the __PE Master__ group, click the __Classes__ tab, and find the __pe_repo__ class.
2. From the __Parameter__ drop-down list, select __master__.
3. In the __Value__ field, enter the address your load balancer resolves to (for example, `loadbalancer.example.com`).
4. Click __Add parameter__ and then the __Commit change__ button.

### Configuring `pe_repo` for Puppet agent installation on compile masters

After configuring `pe_repo` to point newly installed Puppet agents to the load balancer, you need to make an additional classification change so that any newly provisioned compile masters will point to the MoM instead of the load balanced compile masters.

**To point new compile masters at the MoM**:

1. From the console, click __Nodes__ > __Classification__.
2. From the __Classification__ page, in the __Node group name__ field, enter a name such as "PE CA pe\_repo override." (Note, do not add the quotes.)
3. From the __Parent name__ drop-down list, select the __PE Master__ group, and then click __Add group__.
4. In the __PE CA pe_repo override__ group, from the __Rules__ tab, in the __Node name__ field, enter the certname of your CA server.
5. Click the __Classes__ tab, and in the __Add new class__ field, enter "pe\_repo," and select __Add class__. (Note, do not add the quotes.)
6. From the __Parameter__ drop-down list, select __master__.
7. In the __Value__ field, enter the FQDN of your original Puppet master/CA server (e.g. in the examples above, `master.example.com`).
8. Click __Add parameter__ and then the __Commit change__ button.