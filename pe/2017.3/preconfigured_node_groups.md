# Preconfigured node groups

Puppet Enterprise includes preconfigured node groups that are used to manage your configuration.

## **All Nodes** node group

This node group is at the top of the hierarchy tree. All other node groups stem from this node group.

-   **Classes**

    No default classes. Avoid adding classes to this node group.

-   **Matching nodes**

    All nodes.

-   **Notes**

    You can't modify the preconfigured rule that matches all nodes.


## Infrastructure node groups

Infrastructure node groups are used to manage PE.

**Important:** Don't make changes to infrastructure node groups other than pinning new nodes for documented functions, like creating compile masters. If you want to add custom classifications to infrastructure nodes, create new child groups and apply classification there.

### **PE Infrastructure** node group

This node group is the parent to all other infrastructure node groups.

The **PE Infrastructure** node group contains data such as the hostnames and ports of various services and database info \(except for passwords\).

It's very important to correctly configure the `puppet_enterprise` class in this node group. The parameters set in this class affect the behavior of all other preconfigured node groups that use classes starting with `puppet_enterprise::profile`. Incorrect configuration of this class could potentially cause a service outage.

CAUTION:

Never remove the **PE Infrastructure** node group. Removing the **PE Infrastructure** node group disrupts communication between all of your **PE Infrastructure** nodes.

-   **Classes**

    `puppet_enterprise` — sets the default parameters for child node groups

-   **Matching nodes**

    Nodes are not pinned to this node group. The **PE Infrastructure** node group is the parent to other infrastructure node groups, such as **PE Master**, and is only used to set classification that all child node groups inherit. Never pin nodes directly to this node group.


These are the parameters for the `puppet_enterprise` class.

In a monolithic install, `<YOUR HOST>` is your master certname. You can find the certname with `puppet config print certname`. In a split install, `<YOUR HOST>` is the certname of the server on which you installed the component.

|Parameter|Value|
|---------|-----|
|`mcollective_middleware_hosts`|`["<YOUR HOST>"]`

 This value must be an array, even if there is only one value, for example `mcollective_middleware_hosts = ["master.testing.net"]`

|
|`database_host`|`"<YOUR HOST>"`|
|`puppetdb_host`|`"<YOUR HOST>"`|
|`database_port`|`"<YOUR PORT NUMBER>"`

 Required only if you changed the port number from the default 5432.

|
|`database_ssl`|`true` if you're using the PE-installed PostgreSQL, and `false` if you're using your own PostgreSQL.|
|`puppet_master_host`|`"<YOUR HOST>"`|
|`certificate_authority_host`|`"<YOUR HOST>"`|
|`console_port`|`"<YOUR PORT NUMBER>"`

 Required only if you changed the port number from the default 443.\)

|
|`puppetdb_database_name`|`"pe-puppetdb"`|
|`puppetdb_database_user`|`"pe-puppetdb"`|
|`puppetdb_port`|`"<YOUR PORT NUMBER>"`

 Required only if you changed the port number from the default 8081.\)

|
|`console_host`|`"<YOUR HOST>"`|
|`pcp_broker_host`|`"<YOUR HOST>"`|

### **PE Certificate Authority** node group

This node group is used to manage the certificate authority.

-   **Classes**

    `puppet_enterprise::profile::certificate_authority` — manages the certificate authority on the first master node

-   **Matching nodes**

    On a new install, the master is pinned to this node group.

-   **Notes**

    Don't add additional nodes to this node group.


### **PE MCollective** node group

This node group is used to enable the MCollective engine on all matching nodes.

-   **Classes**

    `puppet_enterprise::profile::mcollective::agent` — manages the MCollective server

-   **Matching nodes**

    All nodes.

-   **Notes**

    You might have some nodes, such as non-root nodes or network devices, that should not have MCollective enabled. You can create a rule in this node group to exclude these nodes.


### **PE Master** node group

This node group is used to manage masters and add compile masters.

-   **Classes**

    -   `puppet_enterprise::profile::master` — manages the Puppet master service
    -   `puppet_enterprise::profile::mcollective::peadmin` — manages the peadmin MCollective client
    -   `puppet_enterprise::profile::master::mcollective` — manages keys used by MCollective
-   **Matching nodes**

    On a new install, the master is pinned to this node group.


**Related topics**  


[Install compile masters](installing_compile_masters.md#)

### **PE Orchestrator** node group

This node group is used to manage the application orchestration service.

-   **Classes**

    `puppet_enterprise::profile::orchestrator` — manages the application orchestration service

-   **Matching nodes**

    On a new install, the master is pinned to this node group.

-   **Notes**

    Don't add additional nodes to this node group.


### **PE PuppetDB** node group

This node group is used to manage the database service.

-   **Classes**

    `puppet_enterprise::profile::puppetdb` — manages the PuppetDB service

-   **Matching nodes**

    On a new install, the PuppetDB server node is pinned to this node group.

-   **Notes**

    Don't add additional nodes to this node group.


### **PE Console** node group

This node group is used to manage the console.

-   **Classes**

    -   `puppet_enterprise::profile::console` — manages the console, node classifier, and RBAC
    -   `puppet_enterprise::license` — manages the PE license file for the status indicator
-   **Matching nodes**

    On a new install, the console server node is pinned to this node group.

-   **Notes**

    Don't add additional nodes to this node group.


### **PE ActiveMQ Broker** node group

This node group is used to manage the ActiveMQ broker and add additional ActiveMQ brokers.

-   **Classes**

    `puppet_enterprise::profile::amq::broker` — manages the ActiveMQMCollective broker

-   **Matching nodes**

    On a new install, the master is pinned to this node group.


**Related topics**  


[Install ActiveMQ hubs and spokes](installing_activemq_hubs_and_spokes.md#)

### **PE Agent** node group

This node group is used to manage the configuration of agents.

-   **Classes**

    `puppet_enterprise::profile::agent` — manages your agent configuration

-   **Matching nodes**

    All managed nodes are pinned to this node group by default.


### **PE Infrastructure Agent** node group

This node group is a subset of the **PE Agent** node group used to manage infrastructure-specific overrides.

-   **Classes**

    `puppet_enterprise::profile::agent` — manages your agent configuration

-   **Matching nodes**

    All nodes used to run your Puppet infrastructure and managed by the PE installer are pinned to this node group by default, including the master, PuppetDB, console, and compile masters.

-   **Notes**

    You might want to manually pin to this group any additional nodes used to run your infrastructure, such as compile master load balancer nodes. Pinning a compile master load balancer node to this group allows it to receive its catalog from the master of masters, rather than the compile master, which helps ensure availability.


## Environment node groups

Environment node groups are used only to set environments. They should not contain any classification.

Environment node groups force all matching nodes into the group's environment, even if those nodes match another node group that specifies a different environment. This workflow avoids the environment conflicts that can happen when you unintentionally have nodes that match multiple node groups with conflicting environments.

**Note:** Don't uncheck **This is an environment group** for these preconfigured environment node groups.

### Production environment node group

Nodes in this group will be assigned to the production environment.

-   **Classes**

    You should never add any classes to this group. This group should only be used to set the production environment for matching nodes.

-   **Matching nodes**

    This group comes with a rule that matches all nodes.

-   **Notes**

    The **This is an environment group** option must be selected in the node group metadata section.


### Agent-specified environment node group

Normally, an environment specified by the node classifier overrides any environment set in a node's own `puppet.conf` file \(the *agent-specified environment*\). However, sometimes you might want to let an agent specify its own environment, such as in an environment-based testing workflow.

The agent-specified environment node group forces the environment specified in a node's `puppet.conf` file, ignoring any environments that have been set by the node classifier. If you want to use the agent-specified environment for a node, you should pin the node to this node group, or create a rule in the node group that matches the node.

-   **Classes**

    Don't add any classes to this group. This group should only be used to set the agent-specified environment for matching nodes.

-   **Matching nodes**

    By default, this group matches no nodes. Create rules to match nodes that should be assigned the agent-specified environment. Alternatively, you can manually pin the nodes to the group.

-   **Notes**

    The **This is an environment group** option must be selected in the node group metadata section.


