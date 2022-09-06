# Start adding classes

In this section, you'll use the console to add a class to your Puppet agent. Classes are named chunks of Puppet code that are stored in modules. The class you assign in this exercise is derived from the module you installed previously.

The Puppet `wsus_client` module contains a class called `wsus_client`. In this exercise, you'll use the `wsus_client` class to supply the types and providers necessary to schedule updates using a WSUS server with Puppet.

To prepare the class, you create a group called `windows_example` and add the `wsus_client` class to it.

**Note:** The process for adding classes to agent nodes in the console is the same on both Windows and \*nix operating systems.

## Step 1: Create the `windows_example` group

To classify a node is to add classification data \(classes, parameters, and variables\) for the node group to the node.

### Before you begin

These instructions assume you have installed a monolithic PE deployment, installed at least one Windows agent node, and installed the `puppetlabs-wsus_client` module.

### Procedure

1.  In the console, click **Classification**, and click **Add group**.

2.  In the **Group name** field, name your group, for example, `windows_example`, and click **Add**.

3.  Click **Add membership rules, classes, and variables**.

4.  On the **Rules** tab, in the **Certname** field, enter the name of the managed node you want to add to this group, and click **Pin node**.

    Repeat this step for any additional nodes you want to add.

    **Note:** Pinning a node adds the node to the group regardless of any rules specified for a node group. A pinned node remains in the node group until you manually remove it. Adding nodes dynamically describes how to use rules to add nodes to a node group.

5.  Commit your changes.


## Step 2: Add the `wsus_client` class to the example group

Adding the class to the group and then running Puppet configures the group to use that class.

### Procedure

1.  In the console, click **Classification**, and find and select the `windows_example` group.

2.  On the **Configuration** tab, in the **Add new class** field, select `wsus_client`.

    If `wsus_client` doesn't appear in the list, you might have to click **Refresh**.

3.  Click **Add class**, and commit changes.

    The `wsus_client` class now appears in the list of classes for your agent node.

4.  Puppet runs, which configures the `windows_ example` group using the newly-assigned class. Wait one or two minutes.


