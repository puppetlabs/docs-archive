---
layout: default
title: "Adding classes quick start guide"
canonical: "/pe/latest/quick_start_adding_class_nix.html"
---


## Overview

Every module contains one or more **classes**. [Classes]({{puppet}}/lang_classes.html) are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes. The puppetlabs-apache module you installed in the [Module installation quick start guide](./quick_start_module_install_nix.html) contains a class called `apache`. In this exercise, you'll use the `apache` class to launch the default Apache virtual host. You'll create a group called __apache_example__ and add the `apache` class to it.

> **Prerequisites**: This guide assumes you've already [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [*nix agent node](./quick_start_install_agents_nix.html) and the [puppetlabs-apache module](./quick_start_module_install_nix.html).


## Create the apache_example group

1. In the PE console, click **Nodes** > **Classification**, then click **Add group**.
2. Specify options for the new node group:
   - **Parent name** -- Select **All Nodes**.
   - **Group name** -- Enter a name that describes the role of this environment node group, for example, *apache_example*.
   - **Environment** -- Select **production**.
   - **Environment group** -- Don't select this option.
3. Click **Add**.
4. Click the *apache_example* group.
5. From the **Rules** tab, in the **Certname** area, in the **Node name** field, enter the name of the PE-managed node you'd like to add to this group.
6. Click **Pin node**.
7. Click **Commit 1 change**.
8. Repeat steps 5-7 for any other nodes you want to add.

> **Tip**: Adding nodes to groups by pinning individual nodes is referred to as adding nodes "statically." In more complex deployments, beyond the scope of this QSG, you can [add nodes "dynamically"](./console_classes_groups.html#adding-nodes-dynamically) by setting rules based on node facts.


## Add the `apache` class to the example group

Unless you have navigated elsewhere in the console, the __apache_example__ node group should still be displayed in the __Classification__ page.

**To add the `apache` class to the example group:**

1. Click the __Classes__ tab.

2. In the __Class name__ field, begin typing `apache`, and select it from the autocomplete list.

3. Click __Add class__ and then click __Commit 1 change__.

   The `apache` class now appears in the list of classes for your agent. You can see this list by clicking __Nodes__ > __Inventory__ and then clicking your node in the __Inventory__ list. When the page opens with your node's details, click the **Classes** tab.

4. From the command line on your agent, run `puppet agent -t`. Next, navigate to `/var/www/html/`, and create a file named `index.html` if it doesn't already exist.

5. Open `index.html` with the text editor of your choice and fill it with some content (for example, "Hello World") or edit what is already there.

6. From the command line of each PE-managed node, run `puppet agent -t`.

   This configures the node using the newly assigned class. Wait one or two minutes.

7. Open a browser and enter the IP address for the agent node, adding port 80 on the end, as in `http://myagentnodeIP:80/`.

   The contents of `/var/www/html/index.html` are displayed.

> Puppet Enterprise is now managing the default Apache vhost on your agent node. See the Apache ReadMe on the Forge to explore options for managing your Apache instances. The [*Nix module writing quick start guide](./quick_writing_nix.html) discusses how to write your own class that manages a web app running on an Apache virtual host.

## Editing class parameters in the console

You can use the console to modify the values of a class's parameters without editing the module code directly.

**To edit the parameters of the** `apache` **class**:

1. In the console, click __Nodes__ > __Classification__.
2. Click the __apache_example__ group.
3. Click the __Classes__ tab, and find `apache` in the list of classes.

4. From the __Parameter Name__ drop-down list, choose the parameter you’d like to edit. For example, `docroot`.

   **Note**: The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by selecting __Discard changes__ after you have added the parameter.

5. In the __Value__ field, enter `/var/www`.
6. Click __Add parameter__.
7. Click __Commit 1 change__.
8. From the command line of each PE-managed node, run `puppet agent -t`.

   This triggers a Puppet run, and Puppet Enterprise creates the new configuration.

**That's it!** You have set the Apache web server's root directory to `/var/www` instead of its default `/var/www/html`. If you refresh `http://myagentnodeIP:80/`in your web browser, it shows the list of files in `/var/www`. If you click `html`, the browser again shows the contents of `/var/www/html/index.html`.

----------

Next: [Classifying nodes and assigning user permissions](./quick_start_nc_rbac.html)
