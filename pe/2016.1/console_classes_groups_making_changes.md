---
layout: default
title: "Making changes to node groups"
canonical: "/pe/latest/console_classes_groups_making_changes.html"
---


[environment_override]: ./console_classes_groups_environment_override.html

## Editing node groups

You can change the following node group details:

- the name of the node group
- the environment that the node group assigns to matching nodes
- the [This is an environment group][environment_override] setting
- the node group’s parent node group
- the description of the node group

**To edit a node group:**

1. Click **Nodes** > **Classification**.
2. To go to the node group details, click the node group that you want to edit.
3. At the top right of the page, click **Edit node group metadata**.
4. When you are ready to commit changes, click the commit button.

## Deleting node groups

**To delete a node group:**

1. Click **Nodes** > **Classification**.

2. Click the node group that you want to delete.

3. At the top right of the page, click **Remove node group**.

    > **Note:** A node group can only be deleted if it has no children.

## Editing rules

**To edit an existing rule:**

1. Click **Nodes** > **Classification**.

2. Click the node group containing the rule that you want to edit.

3. In the **Rules** tab, find the rule that you want to edit.

4. In the row for that rule, click **Edit** and make the changes.

5. When you are ready to commit changes, click the commit button.

## Removing nodes from a node group

Nodes are automatically removed from a node group when they no longer match the rules that have been declared in the node group. You need to either edit or delete the rule that was used to include the node in the node group. If the node has been pinned to the node group, you can remove it from the node group by unpinning it.

**To delete a rule:**

1. Click **Nodes** > **Classification**.

2. Click the node group containing the rule that you want to edit.

3. In the **Rules** tab, find the rule that you want to edit.

4. To delete the rule, click **Remove**.

   You can also click **Remove all** if you want to delete all rules for this node group. The rule stays in the list with a line through it until you commit your changes.

5. When you are ready to commit changes, click the commit button.

    > **Note:** When a node no longer matches the rules of a node group, it is no longer classified with the classes assigned in that node group. However, the resources that were installed by those classes are not removed from the node. For example, if a node group has the `apache` class that installs the Apache package on matching nodes, the Apache package is not removed from the node even when the node no longer matches the node group rules.

**To unpin a node from a single group:**

1. In the **Rules** tab, find the node that you want to unpin. You will find the pinned nodes under the list of rules.

2. To unpin the node, click **Unpin**.

   You can also unpin all pinned nodes by clicking **Unpin all pinned nodes**. The node stays in the list with a line through it until you commit your changes.

3. When you are ready to commit changes, click the commit button.

    > **Note:** To unpin a node from all groups it's pinned to, use the [`unpin-from-all` command endpoint](./nc_commands.html#post-v1commandsunpin-from-all).

## Removing classes from a node group

**To remove a class from a node group:**

1. Click **Nodes** > **Classification**.

2. Click the node group containing the class that you want to remove.

3. In the **Classes** tab, find the class that you want to remove.

4. To remove the class, at the bottom of the list of parameters, click **Remove this class**.

   You can also click **Remove all classes** if you want to remove all classes for this node group. The class stays in the list with a line through it until you commit your changes.

5. When you are ready to commit changes, click the commit button.

**Note:** If a class appears in the list but is crossed out, the class has been deleted from Puppet.

## Editing parameters

**To edit the parameters of a class:**

1. Click **Nodes** > **Classification**.

2. Click the node group containing the parameter that you want to change.

3. In the **Classes** tab, find the class and parameter.

4. To edit the parameter, click **Edit** and select another parameter or change the value of the parameter.

5. When you are ready to commit changes, click the commit button.

## Deleting parameters

**To delete a parameter that has been set for a class:**

1. Click **Nodes** > **Classification**.

2. Click the node group containing the parameter that you want to delete.

3. In the **Classes** tab, find the class and parameter.

4. To delete the parameter, click **Remove**. The parameter stays in the list with a line through it until you commit your changes.

5. When you are ready to commit changes, click the commit button.

## Editing variables

**To edit a variable that has been set in a node group:**

1. Click **Nodes** > **Classification**.

2. Click the node group containing the variable that you want to change.

3. In the **Variables** tab, find the variable.

4. To edit the variable, click **Edit** and make your changes.

5. When you are ready to commit changes, click the commit button.

## Deleting variables

**To delete a variable that has been set in a node group:**

1. Click **Nodes** > **Classification**.

2. Click the node group containing the variable that you want to change.

3. In the **Variables** tab, find the variable.

4. To delete the variable, click **Remove**.

   You can also click **Remove all variables** if you want to remove all variables for this node group.The variable stays in the list with a line through it until you commit your changes.

5. When you are ready to commit changes, click the commit button.



