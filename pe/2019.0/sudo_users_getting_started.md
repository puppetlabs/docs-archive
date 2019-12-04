# Managing sudo with PE

Managing sudo on your agent nodes allows you to control which system users have access to elevated privileges. This guide provides instructions for getting started managing sudo privileges across your nodes, using a module from the Puppet Forge in conjunction with a simple module you write.

In most cases, you want to manage sudo on your nodes to control which system users have access to elevated privileges.

## Managing sudo overview

To manage sudo configuration and privileges across your deployment, you write a module.

The `saz-sudo` module, available on the Puppet Forge, is one of many modules written by a member of our user community. To learn more about the module, visit the Puppet Forge.

You also write a simple `privileges` module with a class to manage sudo privileges. The `saz-sudo` module has several classes, but the module you write contains just one.

-   Classes
-   `saz-sudo` on Puppet Forge.

Using this guide, you:

-   Install the `saz-sudo` module as the foundation for your management of sudo privileges.
-   Write a simple `privileges` module to manage a few resources that set privileges for certain users, which are managed by the `saz-sudo` module.
-   Create a **Sudo** node group
-   Add classes from the `privileges` and `sudo` modules to your agent nodes in the console.

These instructions assume you have installed PE. Refer to the installation overview and the agent installation instructions for complete instructions. See the supported operating system documentation for supported platforms. This guide assumes that you are *not* using Code Manager or r10k.

**Note:** Follow the instructions in the NTP getting started guide to have PE ensure time is in sync across your deployment.

## About module directories

By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules that you download from the Forge and those you write yourself.

PE also creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. For this guide, don't modify or add anything to either of these directories.

There are plenty of resources about modules and the creation of modules that you can reference.

Related topics:

-   Puppet: Module fundamentals.
-   Puppet: The modulepath.
-   The Beginner's guide to modules.
-   The Puppet Forge.

## Install the saz-sudo module

To start managing sudo configuration with Puppet Enterprise, install the `saz-sudo` module.

### About this task

### Procedure

1.  From the master, run `puppet module install saz-sudo`.

    You should see output similar to the following:

    ```
    Preparing to install into /etc/puppetlabs/code/environments/production/modules ... Notice: Downloading from http://forgeapi.puppetlabs.com ... Notice: Installing -- do not interrupt ... /etc/puppetlabs/code/environments/production/modules └── saz-sudo (v2.3.6) └── puppetlabs-stdlib (3.2.2) [/opt/puppetlabs/puppet/modules]
    ```


### Results

That's it! You've just installed the `saz-sudo` module. Wait a short time for the Puppet server to refresh before the classes are available to add to your agents.

## Write the privileges module

Manage sudo privileges with Puppet Enterprise, by writing a `privileges` module.

### About this task

The `privileges` module contains the following files:

-   **privileges/** \(the module name\)

    -   **manifests/**

        -   **init.pp** \(contains the privileges class\)


### Procedure

1.  From the command line on the master, navigate to the modules directory:

    `cd /etc/puppetlabs/code/environments/production/modules`

2.  Run `mkdir -p privileges/manifests`to create the new module directory and its manifests directory.

3.  From the `manifests` directory, use your text editor to create the `init.pp` file, and edit it so that it contains the following Puppet code.

    ```
    class privileges { user { 'root': ensure => present, password => '$1$oST1TkX7$p21hU2qzMkR4Iy7HK6zWq0', shell => '/bin/bash', uid => '0', } sudo::conf { 'admins': ensure => present, content => '%admin ALL=(ALL) ALL', } sudo::conf { 'wheel': ensure => present, content => '%wheel ALL=(ALL) ALL', } }
    ```

4.  Save and exit the file.


### Results

That's it! You've written a module that contains a class that, when applied, ensures that your agent nodes have the correct sudo privileges set for the root user and the "admin" and "wheel" groups. You add this class at the same time you add the `saz-sudo` module.

To learn more about what the resources in this class do, see the related topic about the resources in the `privileges` class.

## About the resources in the privileges class

The `privileges` module you wrote for managing sudo privileges in your deployment contains just one class, but several resources. Each resource has a specific job.

The `privileges` module contains the following resources:

-   `user 'root'`: This resource ensures that the root user has a centrally defined password and shell. Puppet enforces this configuration and report on and remediate any drift detected, such as if a rogue admin logs in and changes the password on an agent node.

-   `sudo::conf 'admins'`: Create a sudoers rule to ensure that members of the admin group have the ability to run any command using sudo. This resource creates configuration fragment file to define this rule in `/etc/sudoers.d/`. It is usually called something like `10_admins`.

-   `sudo::conf 'wheel'`: Create a sudoers rule to ensure that members of the wheel group have the ability to run any command using sudo. This resource creates a configuration fragment to define this rule in `/etc/sudoers.d/`. It is usually called something like `10_wheel`.


## Create the Sudo node group

To specify which nodes you want to manage sudo on, set up a designated node group.

### About this task

This group, called Sudo, contains all of your nodes. Depending on your needs or infrastructure, your group might be different.

### Procedure

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group:

    -   **Parent name**: select **All nodes**
    -   **Group name**: enter a name that describes the role of this environment node group
    -   **Environment**: select **production**

    -   **Environment group**: don't select this option

3.  Click **Add**

4.  Click the `Sudo` group and select the **Rules** tab.

5.  In the **Fact** field, enter name.

6.  From the **Operator**drop-down list, select **~** \(matches regex\).

7.  In the **Value** field, enter `.\*`.

8.  Click **Add rule**.


### Results

This rule "dynamically" pins all nodes to the Sudo group. Note that this rule is for testing purposes and that decisions about pinning nodes to groups in a production environment vary. To learn more, see the related topic about dynamically pinning nodes.

Related topics: Adding nodes "dynamically"

## Add the privileges and sudo classes

To manage sudo configuration and privileges for the nodes in your Sudo group, add the `privileges` and `sudo` classes to your node group.

### About this task

The `privileges` module you wrote has only one class \(`privileges`\), but the `saz-sudo` module contains several classes. If you don't want to add these classes to all of your nodes, you can pin the nodes "statically" or write a different rule to add them "dynamically", depending on your needs. See the related topics about adding nodes dynamically or statically for more information.

### Procedure

1.  In the console, click **Classification**, and find and select the `Sudo` group.

2.  On the **Configuration** tab, in the **Class name** field, enter sudo.

3.  Click **Add class**, and commit changes.

    **Note:** The `sudo` class now appears in the list of classes for the Sudo group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

4.  Repeat steps 2 and 3 to add the `privileges` class.

5.  From the command line of your master, run `puppet agent -t`.

6.  From the command line of each PE-managed node, run `puppet agent -t`.


### Results

This configures the nodes using the newly-assigned classes. Wait one or two minutes.

Congratulations! You’ve just created the `privileges` class that you can use to define and enforce a sudoers configuration across your PE-managed infrastructure.

