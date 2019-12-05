---
layout: default
title: "DNS quick start guide"
canonical: "/pe/latest/quick_start_dns.html"
---

[downloads]: http://info.puppetlabs.com/download-pe.html
[sys_req]: ./install_system_requirements.html
[agent_install]: ./install_agents.html
[install_overview]: ./install_basic.html

Welcome to the Puppet Enterprise DNS Quick Start Guide. This document provides instructions for getting started managing a simple DNS nameserver file with PE. A nameserver ensures that the "human-readable" names you type in your browser (for example, `google.com`) can be resolved to IP addresses that computers can read.

Sysadmins typically need to manage a nameserver file for internal resources that aren't published in public nameservers. For example, let's say you have several employee-maintained servers in your infrastructure, and the DNS network assigned to those servers use Google's public nameserver located at `8.8.8.8`. However, there are several resources behind your company's firewall that your employees need to access on a regular basis. In this case, you'd build a private nameserver (say at `10.16.22.10`), and then use PE to ensure all the servers in your infrastructure have access to it.

In this exercise, you'll learn how to do the following tasks:

* [Write a simple module that contains a class called `resolver` to manage a nameserver file called, `/etc/resolv.conf`](#write-the-resolver-class).
* [Use the PE console to add the `resolver` class to your agent nodes](#add-the-resolver-class-in-the-console).
* [Change the contents of the nameserver file to see how PE enforces the desired state you specified in the PE console](#enforce-the-desired-state-of-the-resolver-class).


### Install Puppet Enterprise and the Puppet Enterprise agent

If you haven't already done so, install PE. See the [system requirements][sys_req] for supported platforms.

1. [Download and verify the appropriate tarball][downloads].
2. Refer to the [installation overview][install_overview] to determine how you want to install PE, and then follow the instructions provided.
3. Refer to the [agent installation instructions][agent_install] to determine how you want to install your PE agents, and then follow the instructions provided.

>**Tip**: Follow the instructions in the [NTP quick start guide](./quick_start_ntp.html) to have PE ensure time is in sync across your deployment.

>**Note**: You can add the DNS nameserver class to as many agents as needed. For ease of explanation, our console images and instructions might show only one agent.

### Write the `resolver` class

Some modules can be large, complex, and require a significant amount of trial and error, while others, like [PE-supported modules](https://forge.puppetlabs.com/supported), often work right out of the box. This module will be a very simple module to write; it contains just one class and one template.

> #### Note about modules
>
>By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. In a fresh installation, you need to create this modules subdirectory yourself.
>
>**Note**: PE creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. Don't modify anything in or add modules of your own to `/opt/puppetlabs/puppet/modules`. The `/etc/puppetlabs/staging-code/modules` directory is for file sync use only; if you are not using Code Manager or file sync, do not add code to this directory.
>
>There are plenty of resources about modules and the creation of modules that you can reference. Check out [Puppet: Module fundamentals]({{puppet}}/modules_fundamentals.html), [Puppet: The modulepath]({{puppet}}/dirs_modulepath.html), the [Beginner's guide to modules](/guides/module_guides/bgtm.html), and the [Puppet Forge](https://forge.puppetlabs.com/).

Modules are directory trees. For this task, you'll create the following files:

 - `resolver/` (the module name)
   - `manifests/`
      - `init.pp` (contains the `resolver` class)
   - `templates/`
      - `resolv.conf.erb` (contains template for `/etc/resolv.conf` template, the contents of which will be populated after you add the class and run PE.)

**To write the `resolver` class**:

1. From the command line on the Puppet master, navigate to the modules directory: `cd /etc/puppetlabs/code/environments/production/modules`.
2. Run `mkdir -p resolver/manifests` to create the new module directory and its manifests directory.
3. From the `manifests` directory, use your text editor to create the `init.pp` file, and edit it so it contains the following Puppet code.

        class resolver (
          $nameservers,
        ) {

          file { '/etc/resolv.conf':
            ensure  => file,
            owner   => 'root',
            group   => 'root',
            mode    => '0644',
            content => template('resolver/resolv.conf.erb'),
          }
        }

4. Save and exit the file.
5. Run `mkdir -p resolver/templates` to create the templates directory.
6. Use your text editor to create the `resolver/templates/resolv.conf.erb` file.
7. Edit the `resolv.conf.erb` file so that it contains the following ruby code.

        # Resolv.conf generated by Puppet

        <% [@nameservers].flatten.each do |ns| -%>
        nameserver <%= ns %>
        <% end -%>

        # Other values can be added or hard-coded into the template as needed.

8. Save and exit the file.

> That's it! You've written a module that contains a class that will, once applied, ensure your nodes resolve to your internal nameserver. You'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your agents.
>
> Note the following about your new class:
>
> * The class `resolver` ensures the creation of the file `/etc/resolv.conf`.
> * The content of `/etc/resolv.conf` is modified and managed by the template, `resolv.conf.erb`. You will set this content in the next task using the PE console.


### Add the `resolver` class in the console

[classification_selector]: ./images/quick/classification_selector.png

Next, you'll add the `resolver` class to a new node group called **DNS**, which will contain all of your nodes.

The **DNS** group will contain all the nodes in your deployment (including the Puppet master), but you can [create your own groups](./console_classes_groups.html#creating-classification-node-groups) or add the classes to individual nodes, depending on your needs.

**To create the DNS node group**:

1. In the PE console, click **Nodes** > **Classification**, then click **Add group**.
2. Specify options for the new node group:
   - **Parent name** -- Select **default**.
   - **Group name** -- Enter a name that describes the role of this environment node group, for example, *DNS*.
   - **Environment** -- Select **production**.
   - **Environment group** -- Don't select this option.
3. Click **Add**.
4. Click the *DNS* group, then select the **Rules** tab.
5. In the **Fact** field, enter `name`.
6. From the **Operator** drop-down list, select **~** (matches regex).
7. In the **Value** field, enter `.x`.
8. Click **Add rule**.

   This rule will ["dynamically" pin all nodes]((./console_classes_groups.html#adding-nodes-dynamically) to the **DNS** group. Note that this rule is for testing purposes and that decisions about pinning nodes to groups in a production environment will vary from user to user.

**To add the** `resolver` **class to the DNS group**:

1. On the __Classification__ page, select the __DNS__ group.
2. Click the __Classes__ tab.
3. In the __Class name__ field, begin typing `resolver`, and select it from the autocomplete list.
4. Click __Add class__.
5. Click __Commit 1 change__.

   **Note**: The `resolver` class now appears in the list of classes for the __DNS__ group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

7. From the command line of your Puppet master, run `puppet agent -t`.

8. From the command line of each PE-managed node, run `puppet agent -t`.

   This will configure the nodes using the newly-assigned classes. Wait one or two minutes.

> Not done just yet! The `resolver` class now appears in the list of classes for your DNS group, but it has not yet been fully configured. You still need to add the nameserver IP address parameter for the `resolver` class to use. You can do this by adding a parameter right in the console.

#### Add the nameserver IP address parameter in the console

You can add class parameter values to the code in your module, but it’s easier to add those parameters to your classes using the PE console.

**To edit the server parameter of the** `resolver` **class**:

1. In the console, click __Nodes__ in the navigation bar.
2. On the __Classification__ page, select the __DNS__ group.
3. Click the __Classes__ tab, and find `resolver` in the list of classes.
4. From the __parameter__ drop-down list, choose __nameservers__.
5. In the __Value__ field, enter the nameserver IP address you'd like to use (for example, `8.8.8.8`).

   **Note**: The grey text that appears as values for some parameters is the default value, which can be either a literal value or a Puppet variable. You can restore this value by selecting __Discard changes__ after you have added the parameter.

6. Click __Add parameter__.
7. Click __Commit 1 change__.
8. From the command line of your Puppet master, run `puppet agent -t`.
9. From the command line of each PE-managed node, run `puppet agent -t`.

   This will trigger a Puppet run to have Puppet Enterprise create the new configuration.

10. Navigate to `/etc/resolv.conf`. This file now contains the contents of the `resolv.conf.erb` template and the nameserver IP address you added in step 5.

> Success! Puppet Enterprise will now use the nameserver IP address you've specified for that node.
>
> #### Viewing changes on the Events page
>
>The **Events** page lets you view and research changes. You can view changes by **class**, **resource**, or **node**. For example, after applying the `resolver` class, you can use the **Events** page to confirm that changes were indeed made to your infrastructure, most notably that the class created `/etc/resolv.conf` and set the contents as specified by the module's template.
>
>The further you drill down in this page, the more detail you'll receive. If there had been a problem applying the `resolver` class, this information would tell you exactly where that problem occurred or which piece of code you need to fix.
>
>You can click **Reports**, which contains information about the changes made during Puppet runs, including logs and metrics about the run. See [Infrastructure reports](./CM_reports.html) for more info.
>
>For more information about using the **Events** page, see [Working with the Events page](./CM_events.html##working-with-the-events-page).

### Enforce the desired state of the `resolver` class

Finally, let's take a look at how PE will ensure the desired state of the `resolver` class on your agent nodes. In the previous task, you set the nameserver IP address. Now imagine a scenario where a member of your team changes the contents of `/etc/resolv.conf` to use a different nameserver and can no longer access any internal resources.

1. On any agent to which you applied the `resolv.conf` class, edit `/etc/resolv.conf` to be any nameserver IP address other than the one you desire to use.
2. Save and exit the file.
3. Puppet runs.
4. Navigate to `/etc/resolv.conf`, and notice that PE has enforced the desired state you specified for the nameserver IP address.

> That's it! PE has enforced the desired state of your agent node.
>
> And remember, review the changes to the class or node using the **Events** page.

### Other resources

For more information about working with Puppet Enterprise and DNS, check out our [Dealing with name resolution issues](http://puppetlabs.com/blog/resolving-dns-issues) blog post.

Puppet offers many opportunities for learning and training, from formal certification courses to guided online lessons. We've noted a few below; head over to the [learning Puppet page](https://puppetlabs.com/learn) to discover more.

* [Learning Puppet](/learning/) is a series of exercises on various core topics about deploying and using PE.
* The Puppet workshop contains a series of self-paced, online lessons that cover a variety of topics on Puppet basics. You can sign up at the [learning page](https://puppetlabs.com/learn).


