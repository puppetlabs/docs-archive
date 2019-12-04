---
layout: default
title: "Adding classes quick start guide"
canonical: "/pe/latest/quick_start_adding_class_windows.html"
---

### Overview

[windows_add_group]: ./images/quick/windows_add_group.png

Every module contains one or more **classes**. Classes are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes. The Puppet wsus_client module you installed in the [Module Installation QSG](./quick_start_module_install_windows.html) contains a class called `wsus_client`. In this example, you’ll use the `wsus_client` class to supply the types and providers necessary to schedule updates using a WSUS server with Puppet.

In this example, you create a group called __windows_example__ and add the `wsus_client` class to it.

>**Prerequisites**: This guide assumes you've already [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [Windows agent node](./quick_start_install_agents_windows.html) and the [puppetlabs-wsus_client module](./quick_start_module_install_windows.html).

>**Note**: The process for adding classes to agent nodes in the console is the same on both Windows and *nix operating systems.

### Create the windows_example group

1. In the console, click **Nodes** > **Classification**, and click **Add group**.
2. In the __Group name__ field, name your group, for example, *windows_example*, and click **Add**.
3. Click the link to **add membership rules, classes and variables**.
4. On the __Rules__ tab, in the __Certname__ field, enter the name of the managed node you want to add to this group, and click **Pin node**.

   Repeat this step for any additional nodes you want to add.

   > __Note__: Pinning a node adds the node to the group regardless of any rules specified for a node group. A pinned node remains in the node group until you manually remove it. [Adding nodes dynamically](./console_classes_groups.html#adding-nodes-dynamically) describes how to use rules to add nodes to a node group.
5. Commit changes.


   ![adding node to windows group][windows_add_group]


### Add the `wsus_client` class to the example group

1. In the console, click __Nodes__ > __Classification__, and find and select the __windows_example__ group.

2. On the **Classes** tab, in the __Add new class__ field, select __wsus_client__.

   If __wsus_client__ doesn't appear in the list, you might have to click __Refresh__.

3. Click __Add class__, and commit changes.

   The `wsus_client` class now appears in the list of classes for your agent node.

4. Puppet runs, which configures the __windows_ example__ group using the newly-assigned class. Wait one or two minutes.

-------

Next: [Quick Start: Assign User Permissions](./quick_start_rbac_windows.html)

