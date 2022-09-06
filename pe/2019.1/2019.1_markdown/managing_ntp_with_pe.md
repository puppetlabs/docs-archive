# Managing NTP with PE

The clocks on your servers need to synchronize with something to let them know what the right time is. NTP is a protocol that synchronizes computer clocks over a network to within a millisecond, using Coordinated Universal Time \(UTC\). Follow this guide to get time synced across all your PE-managed nodes.

Your entire datacenter, from the network to the applications, depends on accurate time for many different things, such as security services, certificate validation, and file sharing across nodes.

NTP is one of the most crucial, yet easy, services to configure and manage with Puppet Enterprise. Using the Puppet `ntp` module, you can:

-   Ensure time is correctly synced across all the servers in your infrastructure.
-   Ensure time is correctly synced across your configuration management tools.
-   Roll out updates quickly if you need to change or specify your own internal NTP server pool.

The `ntp` module is supported, tested, and maintained by Puppet. You can learn more about the module by visiting the related topic about the ntp module on the Puppet Forge.

The `ntp` module contains several classes. Classes are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes. The `ntp` module contains several classes, but the only class that you use is the `ntp` class. This class includes several other private classes that are for the module's internal use only.

Related topics:

-   Puppet Supported modules
-   The `ntp` module on the Puppet Forge
-   About classes in Puppet

## NTP overview

To get started managing NTP across your infrastructure, install the Puppet `ntp` module and then manage it in the console.

To manage your NTP service, complete the following tasks:

-   Install the `ntp` module.
-   Create an NTP node group
-   Add the ntp class from the module to your agent nodes in the PE console.
-   View changes to your infrastructure in the PE console Events page.
-   Edit parameters of the main NTP class.

These instructions assume you have installed PE. Refer to the installation overview and the agent installation instructions for complete instructions. See the supported operating system documentation for supported platforms. This guide assumes you are *not* using Code Manager or r10k.

## About module directories

By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules that you download from the Forge and those you write yourself.

PE also creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. For this guide, don't modify or add anything to either of these directories.

There are plenty of resources about modules and the creation of modules that you can reference.

Related topics:

-   Puppet: Module fundamentals.
-   Puppet: The modulepath.
-   The Beginner's guide to modules.
-   The Puppet Forge.

## Install the NTP module

Install the `puppetlabs-ntp` module, which helps manage your NTP service.

### About this task

### Procedure

1.  From the command line of your Puppet master, run `puppet module install puppetlabs-ntp`

    You should see output similar to the following:

    ```
    Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
    Notice: Downloading from http://forgeapi.puppetlabs.com ...
    Notice: Installing -- do not interrupt ...
    /etc/puppetlabs/code/environments/production/modules
    └── puppetlabs-ntp (v3.1.2)
    ```


### Results

That's it! You've just installed the `ntp` module. You must wait a short time for the Puppet server to refresh before the classes are available to add to your agent nodes.

## Add the `ntp` class from the module

Using the PE console, you add the module's `ntp` class to a node group that you create, called **NTP**, which contains all of your nodes. Depending on your needs or infrastructure, you might have a different group that you assign NTP to, but these same instructions apply.

### About this task

## Create the NTP node group

The role of a classification node group is to assign classification data, such as classes, parameters, and variables, to nodes .

### About this task

### Procedure

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group:

    -   **Parent name**: select **All nodes**
    -   **Group name**: enter a name that describes the role of this environment node group
    -   **Environment**: select **production**

    -   **Environment group**: don't select this option

3.  Click **Add**

4.  Click the **NTP** group, and select the **Rules**tab.

5.  In the **Fact** field, enter name.

6.  From the **Operator**drop-down list, select **~** \(matches regex\).

7.  In the **Value** field, enter `.\*`.

8.  Click **Add rule**.


### Results

This rule "dynamically" pins all nodes to the **NTP** group. Note that this rule is for testing purposes and that decisions about pinning nodes to groups in a production environment vary from user to user.

## Add the `ntp` class to the NTP group

Node groups contain classes and other elements.

### Procedure

1.  In the console, click **Classification**, and find and select the **NTP** group.

2.  On the **Configuration** tab, in the **Add new class** field, select `ntp`.

    **Tip:** You need to add only the main `ntp` class; it contains the other classes from the module.

3.  Click **Add class**, and commit changes.

    **Note:** The `ntp` class now appears in the list of classes for the **NTP** group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

4.  From the command line of your Puppet master, run `puppet agent -t`.

5.  From the command line of each PE-managed node, run `puppet agent -t`.

    This configures the nodes using the newly-assigned classes.


### Results

**Success!** Puppet Enterprise is now managing NTP on the nodes in the **NTP** group. So, for example, if you forget to restart the NTP service on one of those nodes after running `ntpdate`, PE automatically restarts it on the next Puppet run.

## View ntp changes in the PE console

You can view and research infrastructure changes and events on the console's Events page. After applying the `ntp` class, check the Events page to confirm that changes were indeed made to your infrastructure.

### About this task

Note that in the summary pane on the left, one event, a successful change, has been recorded for Nodes: with events. However, there are two changes for Classes: with events and Resources: with events. This is because the `ntp` class loaded from the `ntp` module contains additional classes---a class that handles the configuration of NTP \(`Ntp::Config`\) and a class that handles the NTP service \(`Ntp::Service`\).

### Procedure

1.  Click Intentional changes in the**Classes: with events** summary view. The main pane shows you that the `Ntp::Config` and `Ntp::Service` classes were successfully added when you ran PE after adding the main `ntp` class.

2.  Navigate through further levels to see more data.


### Results

If you continue to navigate down, you end up at a run summary that shows you the details of the event. For example, you can see exactly which piece of Puppet code was responsible for generating the event. In this case, it was line 15 of the `service.pp` manifest and line 21 of the `config.pp` manifest from the `puppetlabs-ntp` module.

If there had been a problem applying this class, this information would tell you exactly which piece of code you need to fix. In this case, the **Events** page lets you confirm that PE is now managing NTP.

In the upper right corner of the detail pane is a link to a run report, which contains information about the Puppet run that made the change, including logs and metrics about the run. See Infrastructure reports for more information.

For more information about using the Events page, see Working with the Events page.

## Edit parameters of the ntp class

You can edit or add class parameters in the PE console without needing to edit the module code directly.

### About this task

The NTP module, by default, uses public NTP servers. But what if your infrastructure runs an internal pool of NTP servers? You can change the server parameter of the `ntp` class in a few steps using the PE console.

### Procedure

1.  In the console, click **Classification** , and find and select the **NTP** group.

2.  On the **Configuration** tab, find `ntp` in the list of classes.

3.  From the ****Parameter name**** drop-down list, choose `servers`.

    ****

    **Note:** The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by selecting **Discard changes** after you have added the parameter.

4.  In the `Value` field, enter the new server name \(for example, \["time.apple.com"\]\). Note that this should be an array, in JSON format.

5.  Click **Add parameter**, and commit changes.

6.  From the command line of your Puppet master, run`puppet agent -t`.

7.  From the command line of each PE-managed node, run `puppet agent -t`.


### Results

This triggers a Puppet run that causes Puppet Enterprise to create the new configuration.

Puppet Enterprise now uses the NTP server you specified for that node.

**Tip:** Remember to check the **Events** page to be sure the changes were correctly applied to your nodes!

## Learning more about Puppet and the `ntp` module

You can learn more about Puppet and Puppet supported modules.

For more information about working with the Puppet `ntp` module, check out our puppetlabs-ntp: A Puppet Enterprise supported module blog post.

Puppet offers many opportunities for learning and training, from formal certification courses to guided online lessons. Head over to the Learning page to discover more.

