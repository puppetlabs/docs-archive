# Start adding classes

In this guide, you create a new node group, then apply a named chunk of Puppet code --- better known as a class --- to the node group. You apply the `apache` class to your agent node, which allows you to launch the default Apache virtual host on your agent node.

## Complete a basic classification workflow

Classifying nodes associates classes with nodes so that the code they contain can be used by that node.

### Before you begin

These instructions assume you've installed PE, at least one \*nix agent node, and the `puppetlabs-apache` module.

### About this task

Every module contains one or more classes -- a named chunk of Puppet code. Classes are the primary means by which Puppet Enterprise configures nodes. The `puppetlabs-apache`module you installed in the module installation getting started guide contains a class called `apache`. In this section, you apply the `apache` class to your agent node

## Create a node group

Node groups enable you to assign classes to more than one node at a time.

### About this task

You could assign classes to individual nodes one at a time, but chances are, each of your classes needs to be applied to more than one node. By creating a node group, you can apply a class to many nodes at once.

### Procedure

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group:

    -   **Parent name**: select **All nodes**
    -   **Group name**: enter a name that describes the role of this environment node group
    -   **Environment**: select **production**

    -   **Environment group**: don't select this option

3.  Click **Add**


## Step 2: Add nodes to the node group

Add nodes to a node group to manage them more efficiently.

### About this task

To add nodes to a node group, create rules that define which nodes should be included in the group.

### Procedure

1.  Click the `apache_example` group.

2.  From the **Rules** tab, in the **Certname** area, in the **Node name** field, enter the name of your PE agent node.

3.  Click **Pin node**.

4.  Commit changes.

5.  Repeat these steps for any other nodes you want to add to the node group.


## Step 3: Add the apache class to the node group

After you create a node group, add classes to give the matching nodes purpose.

### Procedure

1.  Unless you have navigated elsewhere in the console, the **apache\_example** node group should still be displayed in the **Classification** page. On the **Configuration** tab, in the **Class name** field, begin typing `apache`, and select it from the autocomplete list.

2.  Click **Add class**, and commit changes.

    The `apache` class now appears in the list of classes for your agent. You can see this list by clicking **Inventory** and then clicking your node in the **Inventory** list. When the page opens with your node's details, click the **Configuration** tab.

3.  Apply your changes. From the command line on your agent, run `puppet agent -t`.

4.  To see your changes in action, navigate to `/var/www/html/`, and create a file named `index.html`.

5.  Open `index.html` with the text editor of your choice and add some content \(for example, "Hello, World!"\).

6.  From the command line of your agent node, run `puppet agent -t`. This configures the node using the newly assigned class.

7.  Wait one or two minutes.

8.  Open a browser and enter the IP address for the agent node, adding port 80 on the end, as in `http://myagentnodeIP:80/`. The contents of `/var/www/html/index.html` are displayed.


## Step 4: Edit class parameters in the console

You can use the console to modify the values of a class's parameters without editing the module code directly.

### Procedure

1.  In the console, click **Classification**, and then find and select the `apache_example`.

2.  On the **Configuration** tab, find `apache` in the list of classes.

3.  From the **Parameter name** drop-down list, choose the parameter you want to edit. For this example, select `docroot`.

    **Note:** The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by selecting **Discard changes** after you have added the parameter.

4.  In the **Value** field, enter `/var/www`.

5.  Click **Add parameter**, and commit changes.

6.  From the command line of your PE-managed node, run `puppet agent -t`.

    This triggers a Puppet run, and Puppet Enterprise creates the new configuration.

    You have set the Apache web server's root directory to `/var/www` instead of its default `/var/www/html`. If you refresh `http://myagentnodeIP:80/`in your web browser, it shows the list of files in `/var/www`. If you click `html`, the browser again shows the contents of `/var/www/html/index.html`


### Results

Puppet Enterprise is now managing the default Apache vhost on your agent node. Next, learn how to manage users with PE, and how to set permissions for each user or group of users.

