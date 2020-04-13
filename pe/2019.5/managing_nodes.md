---
author: Claire Cadman <claire.cadman@puppet.com\>
---

# Managing nodes

Common node management tasks include adding and removing nodes from your deployment, grouping and classifying nodes, and running Puppet on nodes. You can also deploy code to nodes using an environment-based testing workflow or the roles and profiles method.

-   **[Adding and removing agent nodes](adding_and_removing_nodes.md#)**  
After you install a Puppet agent on a node, accept its certificate signing request and begin managing it with Puppet Enterprise \(PE\). Or remove nodes that you no longer need.
-   **[Adding and removing agentless nodes](adding_and_removing_nodes_inventory.md#)**  
Using the inventory, you can manage nodes, including devices such as network switches or firewalls, without installing the Puppet agent on them. The inventory stores node and device information securely.
-   **[How nodes are counted](node_count.md)**  
Your node count is the number of nodes in your inventory. Your license limits you to a certain number of active nodes before you hit your bursting limit. If you hit your bursting limit on four days during a month, you must purchase a license for more nodes or remove some nodes from your inventory.
-   **[Running Puppet on nodes](run_puppet_on_nodes.md#)**  
Puppet automatically attempts to run on each of your nodes every 30 minutes. To trigger a Puppet run outside of the default 30-minute interval, you can manually run Puppet.
-   **[Grouping and classifying nodes](grouping_and_classifying_nodes.md#)**  
Configure nodes by assigning classes, parameters, and variables to them. This is called classification.
-   **[Making changes to node groups](making_changes_to_node_groups.md#)**  
 You can edit or remove node groups, remove nodes or classes from node groups, and edit or remove parameters and variables.
-   **[Environment-based testing](environment_based_testing.md#)**  
An environment-based testing workflow is an effective approach for testing new code before pushing it to production.
-   **[Preconfigured node groups](preconfigured_node_groups.md#)**  
Puppet Enterprise includes preconfigured node groups that are used to manage your configuration.
-   **[Designing system configs: roles and profiles](designing_system_configs_roles_and_profiles.md)**  
Your typical goal with Puppet is to build complete system configurations, which manage all of the software, services, and configuration that you care about on a given system. The roles and profiles method can help keep complexity under control and make your code more reusable, reconfigurable, and refactorable.
-   **[Node classifier API v1](node_classifier_service_api.md)**  
These are the endpoints for the node classifier v1 API.
-   **[Node classifier API v2](node_classifier_service_api_v2.md)**  
These are the endpoints for the node classifier v2 API.
-   **[Node inventory API](node_inventory_api.md#)**  
These are the endpoints for the node inventory v1 API.

