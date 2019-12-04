---
layout: default
title: "NTP quick start guide"
canonical: "/pe/latest/quick_start_ntp.html"
---

[downloads]: http://info.puppetlabs.com/download-pe.html
[sys_req]: ./sys_req_os.html
[agent_install]: ./install_agents.html
[install_overview]: ./install_basic.html

The Puppet Enterprise NTP quick start guide gets you started managing an NTP service using the Puppet `ntp` module.

The clocks on your servers are not inherently accurate. They need to synchronize with something to let them know what the right time is. NTP is a protocol designed to synchronize the clocks of computers over a network. NTP uses Coordinated Universal Time (UTC) to synchronize computer clock times to within a millisecond.

Your entire datacenter, from the network to the applications, depends on accurate time for many different things, such as security services, certificate validation, and file sharing across nodes.

NTP is one of the most crucial, yet easiest, services to configure and manage with Puppet Enterprise. Using the Puppet `ntp` module, you can:

* Ensure time is correctly synced across all the servers in your infrastructure.
* Ensure time is correctly synced across your configuration management tools.
* Roll out updates quickly if you need to change or specify your own internal NTP server pool.

The `ntp` module is [supported](http://forge.puppetlabs.com/supported), tested, and maintained by Puppet. You can learn more about the module by visiting [the Puppet Forge](http://forge.puppetlabs.com/puppetlabs/ntp).

The `ntp` module contains several **classes**. [Classes]({{puppet}}/lang_classes.html) are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes. The `ntp` module contains several classes, but the only class that you will use is the `ntp` class. This class includes several other private classes that are for the module's internal use only.

This guide walks you through the following tasks:

* [Install the `ntp` module](#install-the-ntp-module).
* [Create an NTP node group](#create-the-ntp-node-group)
* [Add classes from the module to your agent nodes in the PE console](#add-classes-from-the-module).
* [View changes to your infrastructure in the PE console Events page](#view-changes-in-the-pe-console).
* [Edit parameters of the main NTP class](#edit-parameters-of-the-ntp-class).

Before you begin, you must have installed PE. Refer to the [installation overview][install_overview] and the [agent installation instructions][agent_install] for complete instructions. See the [supported operating system documentation][sys_req] for supported platforms. This guide assumes you are *not* using Code Manager or r10k.

## Install the `ntp` module

Install the `puppetlabs-ntp` module, which helps manage your NTP service.

1. From the command line of your Puppet master, run `puppet module install puppetlabs-ntp`.

You should see output similar to the following:

```
Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
Notice: Downloading from http://forgeapi.puppetlabs.com ...
Notice: Installing -- do not interrupt ...
/etc/puppetlabs/code/environments/production/modules
└── puppetlabs-ntp (v3.1.2)
```

> That's it! You've just installed the `ntp` module. You'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your agent nodes.

## Add classes from the module

Using the PE console, we're going to add the module's `ntp` class to a node group that we'll create, called **NTP**, which will contain all of your nodes. Depending on your needs or infrastructure, you might have a different group that you'll assign NTP to, but these same instructions would apply.

### Create the NTP node group

1. In the PE console, click **Nodes** > **Classification**, and click **Add group**.
2. Specify options for the new node group:
   - **Parent name**: Select **default**.
   - **Group name**: Enter a name that describes the role of this environment node group, for example, *NTP*.
   - **Environment**: Select **production**.
   - **Environment group**: Don't select this option.
3. Click **Add**.
4. Click the **NTP** group, and select the **Rules** tab.
5. In the **Fact** field, enter `name`.
6. From the **Operator** drop-down list, select **~** (matches regex).
7. In the **Value** field, enter `.*`.
8. Click **Add rule**.

   This rule ["dynamically" pins all nodes]((./console_classes_groups.html#adding-nodes-dynamically) to the **NTP** group. Note that this rule is for testing purposes and that decisions about pinning nodes to groups in a production environment will vary from user to user.

### Add the `ntp` class to the NTP group:

1. In the console, click **Nodes** > **Classification**, and find and select the __NTP__ group.

2. On the **Classes** tab, in the __Class name__ field, select `ntp`.

   **Tip**: You only need to add the main `ntp` class; it contains the other classes from the module.

3. Click __Add class__, and commit changes.

   **Note**: The `ntp` class now appears in the list of classes for the __NTP__ group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

4. From the command line of your Puppet master, run `puppet agent -t`.

5. From the command line of each PE-managed node, run `puppet agent -t`.

   This configures the nodes using the newly-assigned classes.

> **Success!** Puppet Enterprise is now managing NTP on the nodes in the __NTP__ group. So, for example, if you forget to restart the NTP service on one of those nodes after running `ntpdate`, PE will automatically restart it on the next Puppet run.

## View changes in the PE console

[EI-default]: ./images/quick/EI_default.png
[EI-class_change]: ./images/quick/EI_class-change.png
[EI-detail]: ./images/quick/EI_detail.png

You can view and research infrastructure changes and events on the console's **Events** page. For example, after applying the `ntp` class, check the **Events** page to confirm that changes were indeed made to your infrastructure.

Note that in the summary pane on the left, one event, a successful change, has been recorded for **Nodes: with events**. However, there are two changes for **Classes: with events** and **Resources: with events**. This is because the `ntp` class loaded from the `ntp` module contains additional classes---a class that handles the configuration of NTP (`Ntp::Config`) and a class that handles the NTP service (`Ntp::Service`).

1. Click __With Changes__ in the __Classes: with events__ summary view. The main pane shows you that the `Ntp::Config` and `Ntp::Service` classes were successfully added when you ran PE after adding the main `ntp` class.

2. Navigate through further levels to see more data.

If you continue to navigate down, you will end up at a run summary that shows you the details of the event. For example, you can see exactly which piece of Puppet code was responsible for generating the event. In this case, it was line 15 of the `service.pp` manifest and line 21 of the `config.pp` manifest from the `puppetlabs-ntp` module.

If there had been a problem applying this class, this information would tell you exactly which piece of code you need to fix. In this case, the **Events** page lets you confirm that PE is now managing NTP.

In the upper right corner of the detail pane is a link to a run report which contains information about the Puppet run that made the change, including logs and metrics about the run. See [Infrastructure reports](./CM_reports.html) for more information.

For more information about using the **Events** page, see [Working with the Events page](./CM_events.html#working-with-the-events-page).

## Edit parameters of the `ntp` class

You can edit or add class parameters in the PE console without needing to edit the module code directly.

The NTP module, by default, uses public NTP servers. But what if your infrastructure runs an internal pool of NTP servers? You can change the server parameter of the `ntp` class in a few steps using the PE console.

1. In the console, click **Nodes** > **Classification**, and find and select the __NTP__ group.

2. On the __Classes__ tab, find `ntp` in the list of classes.

3. From the __parameter__ drop-down list, choose __servers__.

   **Note**: The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by selecting __Discard changes__ after you have added the parameter.

4. In the __Value__ field, enter the new server name (for example, `["time.apple.com"]`). Note that this should be an array, in JSON format.
5. Click __Add parameter__, and commit changes.
6. From the command line of your Puppet master, run `puppet agent -t`.
7. From the command line of each PE-managed node, run `puppet agent -t`.

   This triggers a Puppet run to have Puppet Enterprise create the new configuration.

> Puppet Enterprise will now use the NTP server you've specified for that node.
>
> **Hint**: Remember to check the **Events** page to be sure the changes were correctly applied to your nodes!

## Other resources

For more information about working with the Puppet `ntp` module, check out our [puppetlabs-ntp: A Puppet Enterprise supported module](http://puppetlabs.com/blog/puppetlabs-ntp-puppet-enterprise-supported-module) blog post and our [How to manage NTP](http://puppetlabs.com/webinars/how-manage-ntp) webinar.

Puppet offers many opportunities for learning and training, from formal certification courses to guided online lessons. We've noted a few below; head over to the [learning Puppet page](https://puppetlabs.com/learn) to discover more.

* [Learning Puppet](/learning/) is a series of exercises on various core topics about deploying and using PE.
* The Puppet workshop contains a series of self-paced, online lessons that cover a variety of topics on Puppet basics. You can sign up at the [learning page](https://puppetlabs.com/learn).




