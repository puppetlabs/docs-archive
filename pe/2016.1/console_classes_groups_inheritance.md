---
layout: default
title: "How does inheritance work?"
canonical: "/pe/latest/console_classes_groups_inheritance.html"
---

Node groups inherit classes, class parameter values, variables, and rules from parent node groups. More specifically, inheritance works as follows:

<dl>
<dt>Classes</dt>
<dd>If an ancestor node group has a class, all descendent node groups will also have the class.</dd>
<dt>Variables and class parameters</dt>
<dd>Descendent node groups inherit class parameters and variables from ancestors unless a different value is set for the parameter or variable in the descendent node group. In this case, parameter and variable values set in the descendent node group override values set in ancestor node groups.</dd>
<dt>Rules</dt>
<dd>A node group can only match nodes that all of its ancestors also match. Specifying rules in a child node group is a way of narrowing down the nodes in the parent node group to apply classes to a specific subset of nodes.</dd>
</dl>

**Note:** If you use different values when classifying a node with the same class, parameter, or variable in another node group that shares inheritance, a conflict occurs. This conflict will cause the next Puppet run to fail. To see the classes and variables that have been assigned to a node through other node groups, in the PE console, go to **Nodes** > **Inventory**, click the node you're interested in, and go to the **Classes** or **Variables** tab.

