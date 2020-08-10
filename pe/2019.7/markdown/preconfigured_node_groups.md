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

**Important:** Don't make changes to infrastructure node groups other than pinning new nodes for documented functions, like creating compilers. If you want to add custom classifications to infrastructure nodes, create new child groups and apply classification there.

### **PE Infrastructure** node group

This node group is the parent to all other infrastructure node groups.

The **PE Infrastructure** node group contains data such as the hostnames and ports of various services and database info \(except for passwords\).

It's very important to correctly configure the `puppet_enterprise` class in this node group. The parameters set in this class affect the behavior of all other preconfigured node groups that use classes starting with `puppet_enterprise::profile`. Incorrect configuration of this class could potentially cause a service outage.

CAUTION:

Never remove the **PE Infrastructure** node group. Removing the **PE Infrastructure** node group disrupts communication between all of your **PE Infrastructure** nodes.

-   **Classes**

    `puppet_enterprise` — sets the default parameters for child node groups

-   **Matching nodes**

    Nodes are not pinned to this node group. The **PE Infrastructure** node group is the parent to other infrastructure node groups, such as **PE Master**, and is used only to set classification that all child node groups inherit. Never pin nodes directly to this node group.


These are the parameters for the `puppet_enterprise` class, where `<YOUR HOST>` is your master certname. You can find the certname with `puppet config print certname`.

|Parameter|Value|
|---------|-----|
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


### **PE Master** node group

This node group is used to manage the Puppet master.

-   **Classes**

    -   `puppet_enterprise::profile::master` — manages the Puppet master service
-   **Matching nodes**

    On a new install, the master is pinned to this node group.


### **PE Compiler** node group

This node group is a subset of the **PE Master** node group used to manage compilers running the PuppetDB service.

-   **Classes**

    -   `puppet_enterprise::profile::master` — manages the Puppet master service
    -   `puppet_enterprise::profile::puppetdb` — manages the PuppetDB service

-   **Matching nodes**

    Compilers running the PuppetDB service are automatically added to this node group.

-   **Notes**

    Don't add additional nodes to this node group.


### **PE Orchestrator** node group

This node group is used to manage the application orchestration service.

-   **Classes**

    `puppet_enterprise::profile::orchestrator` — manages the application orchestration service

-   **Matching nodes**

    On a new install, the master is pinned to this node group.

-   **Notes**

    Don't add additional nodes to this node group.


### **PE PuppetDB** node group

This node group is used to manage nodes running the PuppetDB service. If the node is also serving as a compiler, it's instead classified in the **PE Compiler** node group.

-   **Classes**

    `puppet_enterprise::profile::puppetdb` — manages the PuppetDB service

-   **Matching nodes**

    PuppetDB nodes that aren't functioning as compilers are pinned to this node group.

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

    All nodes used to run your Puppet infrastructure and managed by the PE installer are pinned to this node group by default, including the master, PuppetDB, console, and compilers.

-   **Notes**

    You might want to manually pin to this group any additional nodes used to run your infrastructure, such as compiler load balancer nodes. Pinning a compiler load balancer node to this group allows it to receive its catalog from the master, rather than the compiler, which helps ensure availability.


### **PE Database** node group

This node group is used to manage the PostgreSQL service.

-   **Classes**

    -   `puppet_enterprise::profile::database` — manages the PE-PostgreSQL service
-   **Matching nodes**

    The node specified as `puppet_enterprise::database_host` is pinned to this group. By default, the database host is the PuppetDB server node.

-   **Notes**

    Don't add additional nodes to this node group.


## Environment node groups

Environment node groups are used only to set environments. They cannot contain any classification.

Preconfigured environment node groups differ depending on your version of PE, and you can customize environment groups as needed for your ecosystem.

