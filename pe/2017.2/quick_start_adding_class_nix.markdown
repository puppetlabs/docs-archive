---
layout: default
title: "Adding classes quick start guide"
canonical: "/pe/latest/quick_start_adding_class_nix.html"
---
In this quick start guide, you'll apply the `apache` class to your agent node, which will allow you to launch the default Apache virtual host on your agent node. 

## Complete a basic classification workflow

Every module contains one or more **classes**. [Classes]({{puppet}}/lang_classes.html) are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes. The puppetlabs-apache module you installed in the [Module installation quick start guide](./quick_start_module_install_nix.html) contains a class called `apache`. 

Before you begin, make sure you've [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [*nix agent node](./quick_start_install_agents_nix.html) and the [puppetlabs-apache module](./quick_start_module_install_nix.html).

### Step 1: Create a new node group 

You could assign classes to individual nodes one at a time, but chances are, each of your classes needs to be applied to more than one node. By creating a node group, you can apply a class to many nodes at once. 

1. In the PE console, click **Classification**, and click **Add group**.

2. Specify options for the new node group:
   - **Parent name** -- Select **All Nodes**.
   - **Group name** -- Enter a name that describes the role of this node group, such as *apache_example*.
   - **Environment** -- Select **production**.
   - **Environment group** -- Don't select this option.
   
3. Click **Add**.

### Step 2: Add nodes to the node group

To add nodes to a node group, create rules that define which nodes should be included in the group. 

1. Click the *apache_example* group.
1. From the **Rules** tab, in the **Certname** area, in the **Node name** field, enter the name of your PE agent node.
1. Click **Pin node**.
1. Commit changes.
1. Repeat steps 5-7 for any other nodes you want to add.

>#### FAQs
>
> * [Can I add more than one node at a time to a group?](./console_classes_groups.html#adding-nodes-dynamically)

### Step 3: Add the `apache` class to the node group

Now that you’ve created a node group, add classes to give the matching nodes purpose.

1. Unless you have navigated elsewhere in the console, the __apache_example__ node group should still be displayed in the __Classification__ page. On the **Classes** tab, in the __Class name__ field, begin typing `apache`, and select it from the autocomplete list.

2. Click __Add class__, and commit changes.

The `apache` class now appears in the list of classes for your agent. You can see this list by clicking __Inventory__ and then clicking your node in the __Inventory__ list. When the page opens with your node's details, click the **Classes** tab.

3. Apply your changes. From the command line on your agent, run `puppet agent -t`. 

4. To see your changes in action, navigate to `/var/www/html/`, and create a file named `index.html`.

5. Open `index.html` with the text editor of your choice and add some content (for example, "Hello, World!").

6. From the command line of your agent node, run `puppet agent -t`. This configures the node using the newly assigned class. 
   
7. Wait one or two minutes.

8. Open a browser and enter the IP address for the agent node, adding port 80 on the end, as in `http://myagentnodeIP:80/`. The contents of `/var/www/html/index.html` are displayed.    

### Step 4: Edit class parameters in the console

You can use the console to modify the values of a class's parameters without editing the module code directly.

1. In the console, click __Classification__, and find and select the __apache_example__ group.
2. On the __Classes__ tab, find `apache` in the list of classes.
3. From the __Parameter Name__ drop-down list, choose the parameter you want to edit. For this example, select `docroot`.

   **Note**: The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by selecting __Discard changes__ after you have added the parameter.

4. In the __Value__ field, enter `/var/www`.
5. Click __Add parameter__, and commit changes.
6. From the command line of your PE-managed node, run `puppet agent -t`.

   This triggers a Puppet run, and Puppet Enterprise creates the new configuration.

You have set the Apache web server's root directory to `/var/www` instead of its default `/var/www/html`. If you refresh `http://myagentnodeIP:80/`in your web browser, it shows the list of files in `/var/www`. If you click `html`, the browser again shows the contents of `/var/www/html/index.html`.

> Puppet Enterprise is now managing the default Apache vhost on your agent node. Next, you'll learn how to manage users with PE, and how to set permissions for each user or group of users. [Click here when you're ready to move forward.](./quick_start_nc_rbac.html)  

