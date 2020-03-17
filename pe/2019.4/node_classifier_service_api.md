---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Node classifier API v1

These are the endpoints for the node classifier v1 API.

**Tip:** In addition to these endpoints, you can use the status API to check the health of the node classifier service.

-   **[Forming node classifier requests](forming_node_classifier_requests.md#)**  
Requests to the node classifier API must be well-formed HTTP\(S\) requests.
-   **[Groups endpoint](groups_endpoint.md#)**  
The `groups` endpoint is used to create, read, update, and delete groups.
-   **[Groups endpoint examples](groups_endpoint_examples.md#)**  
Use example requests to better understand how to work with groups in the node classifier API.
-   **[Classes endpoint](classes_endpoint.md#)**  
Use the `classes` endpoints to retrieve lists of classes, including classes with specific environments. The output from this endpoint is especially useful for creating new node groups, which usually contain a reference to one or more classes.
-   **[Classification endpoint](classification_endpoint.md#)**  
The `classification` endpoint takes a node name and a set of facts, and returns information about how that node is classified. The output can help you test your classification rules.
-   **[Commands endpoint](commands_endpoint.md#)**  
Use the commands endpoint to unpin specified nodes from all groups they’re pinned to.
-   **[Environments endpoint](environments_endpoint.md#)**  
Use the `environments` endpoint to retrieve information about environments in the node classifier. The output tells you either which environments are available or whether a named environment exists. The output can be helpful when creating new node groups, which must be associated with an environment. The node classifier gets its information about environments from Puppet, so do not use this endpoint to create, update, or delete them.
-   **[Nodes check-in history endpoint](nodes_endpoint.md#)**  
Use the `nodes` endpoint to retrieve historical information about nodes that have checked into the node classifier.
-   **[Group children endpoint](group_children_endpoint.md#)**  
Use the group children endpoint to retrieve a specified group and its descendents.
-   **[Rules endpoint](rules_endpoint.md#)**  
Use the rules endpoint to translate a group’s rule condition into PuppetDB query syntax.
-   **[Import hierarchy endpoint](import_hierarchy_endpoint.md#)**  
Use the import hierarchy endpoint to delete all existing node groups from the node classifier service and replace them with the node groups in the body of the submitted request.
-   **[Last class update endpoint](last_class_update_endpoint.md#)**  
Use the last class update endpoint to retrieve the time that classes were last updated from the Puppet master.
-   **[Update classes endpoint](update_classes_endpoint.md#)**  
Use update classes endpoint to trigger the node classifier to update class and environment definitions from the Puppet master.
-   **[Validation endpoints](validation_endpoints.md#)**  
Use validation endpoints to validate groups in the node classifier.
-   **[Node classifier errors](node_classifier_errors.md#)**  
Familiarize yourself with error responses to make working the node classifier service API easier.

**Parent topic:**[Managing nodes](managing_nodes.md)

**Related information**  


[API index](api_index.md#)

