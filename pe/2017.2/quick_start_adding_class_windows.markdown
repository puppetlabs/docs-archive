---
layout: default
title: "Adding classes quick start guide"
canonical: "/pe/latest/quick_start_adding_class_windows.html"
---

Every module contains one or more **classes**. Classes are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes. The Puppet wsus_client module contains a class called `wsus_client`. In this exercise, you use the `wsus_client` class to supply the types and providers necessary to schedule updates using a WSUS server with Puppet.

To prepare the class, you create a group called __windows_example__ and add the `wsus_client` class to it.

>**Note**: The process for adding classes to agent nodes in the console is the same on both Windows and *nix operating systems.

<!--Task-->
## Create the windows_example group

To classify a node is to add classification data (classes, parameters, and variables) for the node group to the node.

**Before you begin**: Install a monolithic PE deployment, install at least one Windows agent node, and install the `puppetlabs-wsus_client` module.


1. In the console, click **Classification**, and click **Add group**.
2. In the __Group name__ field, name your group, for example, *windows_example*, and click **Add**.
3. Click **Add membership rules, classes, and variables**.
4. On the __Rules__ tab, in the __Certname__ field, enter the name of the managed node you want to add to this group, and click **Pin node**.

   Repeat this step for any additional nodes you want to add.

   > __Note__: Pinning a node adds the node to the group regardless of any rules specified for a node group. A pinned node remains in the node group until you manually remove it. [Adding nodes dynamically](./console_classes_groups.html#adding-nodes-dynamically) describes how to use rules to add nodes to a node group.
5. Commit changes.

Related topics: [Install Puppet Enterprise quick start guide](./quick_start_install_mono.html), [Install *nix agents quick start guide](./quick_start_install_agents_windows.html), [Module installation quick start guide](./quick_start_module_install_windows.html)

<!--Task-->
## Add the `wsus_client` class to the example group

Adding the class to the group and then running Puppet configures the group to use that class.

1. In the console, click __Classification__, and find and select the __windows_example__ group.

2. On the **Classes** tab, in the __Add new class__ field, select __wsus_client__.

   If __wsus_client__ doesn't appear in the list, you might have to click __Refresh__.

3. Click __Add class__, and commit changes.

   The `wsus_client` class now appears in the list of classes for your agent node.

4. Puppet runs, which configures the __windows_ example__ group using the newly-assigned class. Wait one or two minutes.

-------

Next: [Quick Start: Assign User Permissions](./quick_start_rbac_windows.html)

