# Making changes to node groups

You can edit or remove node groups, remove nodes or classes from node groups, and edit or remove parameters and variables.

## Edit or remove node groups

You can change the name, description, parent, environment, or environment group setting for node groups, or you can delete node groups that have no children.

1.  In the console, click **Classification**, and select a node group.

2.  At the top right of the page, select an option.

    -   **Edit node group metadata** — Enables edit mode so you can modify the node group's metadata as needed.

    -   **Remove node group** — Removes the node group. You're prompted to confirm the removal.

3.  Commit changes.


## Remove nodes from a node group

To remove dynamically-assigned nodes from a node group, edit or delete the applicable rule. To remove statically-assigned nodes from a node group, unpin them from the group.

**Note:** When a node no longer matches the rules of a node group, it is no longer classified with the classes assigned in that node group. However, the resources that were installed by those classes are not removed from the node. For example, if a node group has the `apache` class that installs the Apache package on matching nodes, the Apache package is not removed from the node even when the node no longer matches the node group rules.

1.  In the console, click **Classification**, and select a node group.

2.  On the **Rules** tab, select the option appropriate for the type of node.

    -   **Dynamically-assigned nodes** — In the **Fact** table, click **Remove** to remove an individual rule, or click **Remove all rules** to delete all rules for the node group.

    -   **Statically-assigned nodes** — In the **Certname** table, click **Unpin** to unpin an individual node, or click **Unpin all pinned nodes** to unpin all nodes from the node group.

**Tip:** To unpin a node from all groups it’s pinned to, use the `unpin-from-all` command endpoint.

3.  Commit changes.


**Related information**  


[POST /v1/commands/unpin-from-all](commands_endpoint.md#)

## Remove classes from a node group

Make changes to your node group by removing classes.

**Note:** If a class appears in a node group list but is crossed out, the class has been deleted from Puppet.

1.  In the console, click **Classification**, and select a node group.

2.  On the **Configuration** tab in the **Classes** section, click **Remove this class** to remove an individual class, or click **Remove all classes** to remove all classes from the node group.

3.  Commit changes.


## Edit or remove parameters

Make changes to your node group by editing or deleting the parameters of a class.

1.  In the console, click **Classification**, and select a node group.

2.  On the **Configuration** tab in the **Classes** section, for the class and parameter that you want to edit, select an option.

    -   **Edit** — Enables edit mode so you can modify the parameter as needed. 

    -   **Remove** — Removes the parameter.

3.  Commit changes.


## Edit or remove variables

Make changes to your node group by editing or removing variables.

1.  In the console, click **Classification**, and select a node group.

2.  On the **Variables** tab, select an option.

    -   **Edit** — Enables edit mode so you can modify the variable as needed. 

    -   **Remove** — Removes the variable.

    -   **Remove all variables** — Removes all variables from the node group.

3.  Commit changes.


