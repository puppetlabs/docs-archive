---
layout: default
title: "Making changes to node groups"
canonical: "/pe/latest/console_classes_groups_making_changes.html"
---


[environment_override]: ./console_classes_groups_environment_override.html

## Editing node groups

You can change the name, description, parent, environment, or [environment group setting ][environment_override] for node groups.

1. In the console, click **Classification**, and find and select the node group that you want to modify.
2. At the top right of the page, click **Edit node group metadata**.
3. Edit metadata as needed, and commit changes.


## Deleting node groups

1. In the console, click **Classification**, and find and select the node group that you want to delete.

2. At the top right of the page, click **Remove node group**.

    > **Note:** A node group can only be deleted if it has no children.

3. Commit changes.


## Editing rules

1. In the console, click **Classification**, and find and select the node group that you want to modify.

2. On the **Rules** tab, in the row for the rule that you want to edit, click **Edit**.

3. Edit the rule as needed, and commit changes.


## Removing dynamically-assigned nodes from a node group

Nodes are automatically removed from a node group when they no longer match the rules that have been declared in the node group. You need to either edit or delete the rule that was used to include the node in the node group.

1. In the console, click **Classification**, and find and select the node group that you want to modify.

2. On the **Rules** tab, in the row for the rule that you want to delete, click **Remove**.

   Click **Remove all** if you want to delete all rules for this node group. The rule stays in the list with a line through it until you commit your changes.

3. Commit changes.

   > **Note:** When a node no longer matches the rules of a node group, it is no longer classified with the classes assigned in that node group. However, the resources that were installed by those classes are not removed from the node. For example, if a node group has the `apache` class that installs the Apache package on matching nodes, the Apache package is not removed from the node even when the node no longer matches the node group rules.


## Removing statically-assigned nodes from a node group

If a node was pinned to a node group, you can remove it by unpinning.

1. In the console, click **Classification**, and find and select the node group that you want to modify.

2. On the **Rules** tab, in the row for the node that you want to unpin, click **Unpin**.

   You can also unpin all pinned nodes by clicking **Unpin all pinned nodes**. The node stays in the list with a line through it until you commit your changes.

3. Commit changes.

   > **Note:** To unpin a node from all groups it's pinned to, use the [`unpin-from-all` command endpoint](./nc_commands.html#post-v1commandsunpin-from-all).


## Removing classes from a node group

1. In the console, click **Classification**, and find and select the node group that you want to modify.

2. On the **Classes** tab, under the list of parameters for the class you want to remove, click **Remove this class**.

   You can also click **Remove all classes** if you want to remove all classes for this node group. The class stays in the list with a line through it until you commit your changes.

3. Commit changes.

**Note:** If a class appears in the list but is crossed out, the class has been deleted from Puppet.


## Editing parameters

1. In the console, click **Classification**, and find and select the node group that you want to modify.

2. On the **Classes** tab, click **Edit** for the parameter you want to edit.

3. Modify the parameter as needed, and commit changes.


## Deleting parameters

1. In the console, click **Classification**, and find and select the node group that you want to modify.

2. On the **Classes** tab, click **Remove** for the parameter you want to delete. The parameter stays in the list with a line through it until you commit your changes.

3. Commit changes.


## Editing variables

1. In the console, click **Classification**, and find and select the node group that you want to modify.

3. On the **Variables** tab, click **Edit** for the variable you want to change.

5. Modify the variable as needed, and commit changes.


## Deleting variables

1. In the console, click **Classification**, and find and select the node group that you want to modify.

2. On the **Variables** tab, click **Remove** for the variable you want to delete.

   You can also click **Remove all variables** if you want to remove all variables for this node group.The variable stays in the list with a line through it until you commit your changes.

3. Commit changes.



