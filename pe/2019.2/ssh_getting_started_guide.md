# Manage SSH keys and permissions

Add SSH capabilities to your infrastructure using a module from the Forge.

## SSH overview

Secure Shell \(SSH\) is a protocol that enables encrypted connections between nodes on a network for administrative purposes. This guide provides instructions for getting started managing SSH across your PE deployment using a module from the Puppet Forge.

SSH is an important part of an IT infrastructure. With it, you can access remote systems securely, even through insecure networks, by using keys. This allows you to manage your nodes from one machine.

Typically, the first time you attempt to SSH into a host you’ve never connected to before, you get a warning similar to the following:

```
The authenticity of host '10.10.10.9 (10.10.10.9)' can't be established.
RSA key fingerprint is 05:75:12:9a:64:2f:29:27:39:35:a6:92:2b:54:79:5f.
Are you sure you want to continue connecting (yes/no)?
```

If you select yes, the public key for that host is added to your SSH `known_hosts` file, and you won't have to authenticate it again unless that host's key changes.

You will:

-   Install a module
-   Create a node group
-   Classify nodes
-   Set parameters

## About module directories

By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules that you download from the Forge and those you write yourself.

PE also creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. For this guide, don't modify or add anything to either of these directories.

There are some resources about modules and the creation of modules that you can reference.

-   [Welcome to Puppet modules](https://puppet.com/docs/puppet/latest/modules.html)
-   [Module fundamentals](https://puppet.com/docs/puppet/latest/modules_fundamentals.html)
-   [The Forge](https://forge.puppet.com/)

## Install the ghoneycutt-ssh module

The `ghoneycutt-ssh` module manages SSH keys and removes SSH keys that you aren't managing with Puppet.

### About this task

This module, available on the Puppet Forge, is one of many modules written by our user community. It contains one class: the `ssh` class. You can learn more about the `ghoneycutt-ssh` module by visiting the Forge.

### Procedure

1.  On the command line, on the PE master, run `puppet module install ghoneycutt-ssh -v 3.40.0`.

    The output looks similar to the following:

    ```
    Notice: Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
    Notice: Downloading from https://forgeapi.puppetlabs.com ...
    Notice: Installing -- do not interrupt ...
    /etc/puppetlabs/code/environments/production/modules
    └─┬ ghoneycutt-ssh (v3.40.0)
       ├── ghoneycutt-common (v1.6.0)
       ├── puppetlabs-firewall (v1.8.1)
       └── puppetlabs-stdlib (v4.9.1)
    ```


### Results

That's it! You've just installed the `ghoneycutt-ssh module`. You must wait a short time for the Puppet server to refresh before the classes are available to add to your agent nodes.

## Create the SSH node group

In the console, create a group called ssh\_example to designate which nodes Puppet must manage SSH on.

### About this task

This group contains all of your nodes. Depending on your needs or infrastructure, you might have a different group that you assign SSH to.

### Procedure

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group:

    -   **Parent name**: select **All nodes**
    -   **Group name**: enter a name that describes the role of this environment node group
    -   **Environment**: select **production**

    -   **Environment group**: don't select this option

3.  Click **Add**

4.  Click the `ssh_example` group, then select the **Rules** tab.

5.  In the **Fact** field, enter name.

6.  From the **Operator**drop-down list, select **~** \(matches regex\).

7.  In the **Value** field, enter `.\*`.

8.  Click **Add rule**.


### Results

This rule "dynamically" pins all nodes to the `ssh_example` group. Note that this rule is for testing purposes, and that decisions about pinning nodes to groups in a production environment vary.

Related topics:

Adding nodes dynamically

## Add classes from the ssh module

Add the `ssh` class to the **ssh\_example** node group to add the necessary resources that allow Puppet to manage SSH.

### About this task

Depending on your needs or infrastructure, you might have a different group that you assign SSH to, but these same instructions apply.

After you apply the `ssh` class and run Puppet, the public key for each agent node is exported and then disseminated to the known\_hosts files of the other agent nodes in the group, and you are no longer asked to authenticate those nodes on future SSH attempts.

### Procedure

1.  In the console, click **Classification**, and find and select `ssh_example` group.

2.  On the **Configuration** tab, in the **Class name** field, select `ssh`.

3.  Click **Add class**, and commit changes.

    **Note:** The `ssh` class now appears in the list of classes for the ssh\_example group, but it has not yet been configured on your nodes. For that to happen, kick off a Puppet run.

4.  From the command line of your master, run `puppet agent -t`.

5.  From the command line of each PE-managed node, run `puppet agent -t`.


### Results

This configures the nodes using the newly assigned classes. Wait one or two minutes.

**Important:** You need to run Puppet a second time due to the round-robin nature of the key sharing. In other words, the first server that ran on the first Puppet run was only able to share its key, but it was not able to retrieve the keys from the other agents. It collects the other keys on the second Puppet run.

## View changes made by the ssh class

To confirm that the `ssh` class made changes to your infrastructure, check the **Events**console page. This page lets you view and research changes and other events.

### About this task

For example, after applying the `ssh` class, you can use the **Events**page to confirm that changes were indeed made to your infrastructure.

Note that in the summary pane on the left, one event, a successful change, has been recorded for **Classes: with events**. However, there are three changes for **Classes: with events** and six changes for **Resources: with events**.

### Procedure

1.  Click **With changes** in the **Classes: with events** summary view.

    The main pane shows you that the `ssh` class was successfully added when you ran PE. This class sets the `known_hosts` entries after it collects the public keys from agents nodes in your deployment .

2.  Click **Changed**in the **Resources: with events** summary view.


### Results

The main page shows you that public key resources for each agent in our example has now been brought under PE management. The further down you navigate, the more information you receive about the event. For example, in this case, you see that the the SSH rsa key for `agent1.example.com` has been created and is now present in the `known_hosts` file for `master.example.com`.

If there had been a problem applying any piece of the `ssh` class, the information found here could tell you exactly which piece of code you need to fix. In this case, the **Events**page simply lets you confirm that PE is now managing SSH keys.

In the upper right corner of the detail pane is a link to a run report which contains information about the Puppet run that made the change, including logs and metrics about the run. See Infrastructure reports for more information.

For more information about using the **Events**page, see Working with the Events page.

## Edit root login parameters of the ssh class

Edit or add class parameters from the `ssh` class in the PE console without needing to edit the module code directly.

### About this task

The `ghoneycutt-ssh` module, by default, allows root login over SSH. But if your compliance protocols do not allow this, you can change this parameter of the `ssh` class in a few steps using the PE console.

### Procedure

1.  In the console, click **Classification**, and find and select the **ssh\_example** group.

2.  On the **Configuration** tab, find `ssh` in the list of classes.

3.  From the **parameter** drop-down menu, choose **permit\_root\_login**.

    **Note:** The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by clicking **Discard changes** after you have added the parameter.

4.  In the **Value** field, enter no.

5.  Click **Add parameter**, and commit changes.

6.  From the command line of your master, run `puppet agent -t`.

7.  From the command line of each PE-managed node, run `puppet agent -t`.


### Results

Puppet Enterprise is now managing the root login parameter for your SSH configuration. You can see this setting in `/etc/ssh/sshd_config`. For fun, change the `PermitRootLogin` parameter to `yes`, run PE, and then recheck this file. As long as the parameter is set to `no` in the PE console, the parameter in this file is set back to `no` on every Puppet run if it is ever changed.

You can use the console to manage other SSH parameters, such as agent forwarding, X11 forwarding, and password authentication.

## Learn more about SSH

Check out a few Puppet blog posts to learn more about SSH.

-   [How I stopped worrying and learned to love public key authentication for SSH](https://puppet.com/blog/how-i-stopped-worrying-learned-love-public-key-authentication-ssh)
-   [Speed up SSH by reusing connections](https://puppet.com/blog/speed-up-ssh-by-reusing-connections)
-   [Using Puppet to address new SSH client vulnerability](https://puppet.com/blog/using-puppet-to-address-new-ssh-client-vulnerability)

Next, learn how to create and manage sudo privileges across your infrastructure.

