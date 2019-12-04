---
author: Claire Cadman <claire.cadman@puppet.com\>
---

# Managing nodes

Common node management tasks include adding and removing nodes from your deployment, grouping and classifying nodes, and running Puppet on nodes. You can also deploy code to nodes using an environment-based testing workflow or the roles and profiles method.

-   **[Adding and removing nodes](adding_and_removing_nodes.md#)**  
To manage nodes with Puppet Enterprise \(PE\), you must approve the node’s certificate signing request. If you no longer wish to manage a node, you can remove all traces of it from PE
-   **[Running Puppet on nodes](run_puppet_on_nodes.md#)**  
Puppet automatically attempts to run on each of your nodes every 30 minutes. To trigger a Puppet run outside of the default 30-minute interval, you can manually run Puppet.
-   **[Grouping and classifying nodes](grouping_and_classifying_nodes.md#)**  
Classification involves assigning classes, parameters, and variables to nodes.
-   **[Making changes to node groups](making_changes_to_node_groups.md#)**  
 You can edit or remove node groups, remove nodes or classes from node groups, and edit or remove parameters and variables.
-   **[Environment-based testing](environment_based_testing.md#)**  
An environment-based testing workflow is an effective approach for testing new code before pushing it to production.
-   **[Preconfigured node groups](preconfigured_node_groups.md#)**  
Puppet Enterprise includes preconfigured node groups that are used to manage your configuration.
-   **[Designing system configs: roles and profiles](designing_system_configs_roles_and_profiles.md)**  
Your typical goal with Puppet is to build complete system configurations, which manage all of the software, services, and configuration that you care about on a given system. The roles and profiles method can help keep complexity under control and make your code more reusable, reconfigurable, and refactorable.
-   **[Node classifier service API](node_classifier_service_api.md)**  
These are the endpoints for the node classifier v1 API.

