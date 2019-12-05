---
layout: default
title: "Getting started with classification"
canonical: "/pe/latest/console_classes_groups_getting_started.html"
---

[learn]: /learning/
[forge]: http://forge.puppetlabs.com

## What is classification?

In Puppet Enterprise (PE), you configure your nodes by assigning classes, parameters, and variables to them. This is called *classification*. Classes are blocks of Puppet code that are distributed in the form of modules. You can create your own classes, or you can take advantage of the many classes that have already been created by the amazing Puppet community. To reduce the potential for new bugs and save yourself some time by using the existing classes, see the modules on the [Puppet Forge][forge].

You could assign classes to individual nodes one at a time, but chances are, each of your classes needs to be applied to more than one node &#8212; possibly hundreds or thousands of nodes! This is where *node groups* and *rules* come in. Node groups and rules are a powerful and scalable way to automate the classification of your nodes and save you a lot of manual work.

The main steps involved in dynamically classifying nodes are:

1. Create node groups
2. Create rules to dynamically add and remove nodes from node groups
3. Assign classes to node groups

Nodes can match the rules of many node groups. They receive classes, class parameters, and variables from all the node groups that they match.

Node groups exist in a hierarchy of parent and child node groups. They inherit classes, class parameters, and variables from all ancestor groups. You can override the classification data inherited from ancestor groups by setting new class parameter and variable values in a child group.

## Organizing node groups

A logical approach to organizing your node groups in PE is to start with high-level node groups that reflect the business requirements of your organization. For example, let’s say that a large portion of your infrastructure consists of web servers. There will be some classes, such as the `apache` class, that need to be applied to all of your web servers. You can create a node group called **web servers** and add any classes that need to be applied to all web servers.

Next, you identify subsets of web servers that have common characteristics but differ from other subsets. For example, you may have production web servers and development web servers. You can create a **dev web** child node group under the **web servers** node group. Nodes that match the **dev web** node group will get all of the classes in the parent node group in addition to the classes assigned to the **dev web** node group.

**Tip**: You can use the [group children](./nc_groups_children.html) endpoint to review group lineage.

Additional resources:

* If you are new to Puppet and have not written Puppet code before, [the Learning Puppet tutorial][learn] will help you with the basics of Puppet code, and writing classes and modules.


