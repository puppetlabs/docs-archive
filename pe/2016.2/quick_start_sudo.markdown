---
layout: default
title: "Sudo users quick start guide"
canonical: "/pe/latest/quick_start_sudo.html"
---

[downloads]: http://info.puppetlabs.com/download-pe.html
[sys_req]: ./install_system_requirements.html
[agent_install]: ./install_agents.html
[install_overview]: ./install_basic.html

Welcome to the Puppet Enterprise Sudo Users Quick Start Guide. This document provides instructions for getting started managing sudo privileges across your PE deployment, using a module from the Puppet Forge in conjunction with a simple module you will write.

In most cases, you want to manage sudo on your nodes to control which system users have access to elevated privileges. Using this guide, you will:

* [Install the saz-sudo module as the foundation for your management of sudo privileges](#install-the-saz-sudo-module).
* [Write a simple module that contains a class called `privileges` to manage a few resources that set privileges for certain users, which will be managed by the saz-sudo module](#write-the-privileges-class).
* [Use the PE console to add classes from the privileges and sudo modules to your agent nodes](#use-the-pe-console-to-add-the-privileges-and-sudo-classes).

## Install Puppet Enterprise and the Puppet Enterprise agent

If you haven't already done so, intall PE. See the [system requirements][sys_req] for supported platforms.

1. [Download and verify the appropriate tarball][downloads].
2. Refer to the [installation overview][install_overview] to determine how you want to install PE, and then follow the instructions provided.
3. Refer to the [agent installation instructions][agent_install] to determine how you want to install your PE agents, and then follow the instructions provided.

>**Tip**: Follow the instructions in the [NTP quick start guide](./quick_start_ntp.html) to have PE ensure time is in sync across your deployment.

>**Note**: You can add the sudo and privileges classes to as many agents as needed. For ease of explanation, our console images and instructions might show only two agent nodes.

## Install the saz-sudo module

The saz-sudo module, available on the Puppet Forge, is one of many modules written by a member of our user community.  You can learn more about the module by visiting [the Puppet Forge](http://forge.puppetlabs.com/saz/sudo).

**To install the saz-sudo module**:

From the PE master, run `puppet module install saz-sudo`.

You should see output similar to the following:

        Preparing to install into /etc/puppetlabs/puppet/modules ...
        Notice: Downloading from http://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        /etc/puppetlabs/puppet/modules
        └── saz-sudo (v2.3.6)
              └── puppetlabs-stdlib (3.2.2) [/opt/puppetlabs/puppet/modules]

> That's it! You've just installed the saz-sudo module. You'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your agents.

## Write the `privileges` class

Some modules can be large, complex, and require a significant amount of trial and error as you create them, while others, like [PE-supported modules](https://forge.puppetlabs.com/supported), often work right out of the box. This module will be a very simple module to write. It contains just one class.

> ### Note about modules directories
>
>By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. In a fresh installation, you need to create this modules subdirectory yourself.
>
>**Note**: PE creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. Don't modify anything in or add modules of your own to `/opt/puppetlabs/puppet/modules`. The `/etc/puppetlabs/staging-code/modules` directory is for file sync use only; if you are not using Code Manager or file sync, do not add code to this directory.
>
>There are plenty of resources about modules and the creation of modules that you can reference. Check out [Puppet: Module fundamentals]({{puppet}}/modules_fundamentals.html), [Puppet: The modulepath]({{puppet}}/dirs_modulepath.html), the [Beginner's guide to modules](/guides/module_guides/bgtm.html), and the [Puppet Forge](https://forge.puppetlabs.com/).

Modules are directory trees. For this task, you'll create the following files:

 - `privileges/` (the module name)
   - `manifests/`
      - `init.pp` (contains the `privileges` class)

**To write the `privileges` class**:

1. From the command line on the Puppet master, navigate to the modules directory: `cd /etc/puppetlabs/puppet/modules`.
2. Run `mkdir -p privileges/manifests` to create the new module directory and its manifests directory.
3. From the `manifests` directory, use your text editor to create the `init.pp` file, and edit it so it contains the following Puppet code.

        class privileges {
          user { 'root':
            ensure   => present,
            password => '$1$oST1TkX7$p21hU2qzMkR4Iy7HK6zWq0',
            shell    => '/bin/bash',
            uid      => '0',
          }

          sudo::conf { 'admins':
            ensure  => present,
            content => '%admin ALL=(ALL) ALL',
          }

          sudo::conf { 'wheel':
            ensure  => present,
            content => '%wheel ALL=(ALL) ALL',
          }

        }

5. Save and exit the file.

> That's it! You've written a module that contains a class that, once applied, ensures that your agent nodes have the correct sudo privileges set for the root user and the “admin” and “wheel” groups.
>
> Note the following about the resources in the `privileges` class:
>
> * `user ‘root’`: This resource ensures that the root user has a centrally defined password and shell. Puppet enforces this configuration and report on, and remediate, any drift detected, such as if a rogue admin logs in and changes the password on an agent node.
>
> * `sudo::conf ‘admins’`: Create a sudoers rule to ensure that members of the admin group have the ability to run any command using sudo. This resource creates configuration fragment file to define this rule in `/etc/sudoers.d/`. It will be called something like `10_admins`.
>
> * `sudo::conf ‘wheel’`: Create a sudoers rule to ensure that members of the wheel group have the ability to run any command using sudo. This resource creates a configuration fragment to define this rule in `/etc/sudoers.d/`. It will be called something like `10_wheel`.

You will add this class at the same time you add the saz-sudo module.

## Use the PE console to add the privileges and sudo classes

[classification_selector]: ./images/quick/classification_selector.png

The saz-sudo module contains several **classes**. [Classes]({{puppet}}/lang_classes.html) are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes.

You'll add the `sudo` class to a new node group, called **Sudo**, which will contain all of your nodes. Note that if you don't want to add this class to all of your nodes you can [pin the nodes "statically"](./console_classes_groups.html#adding-nodes-statically) or write a different rule to [add them "dynamically"](./console_classes_groups.html#adding-nodes-dynamically), depending on your needs.

**To create the Sudo node group**:

1. In the PE console, click **Nodes** > **Classification**, then click **Add group**.
2. Specify options for the new node group:
   - **Parent name** -- Select **All Nodes**.
   - **Group name** -- Enter a name that describes the role of this environment node group, for example, *Sudo*.
   - **Environment** -- Select **production**.
   - **Environment group** -- Don't select this option.
3. Click **Add**.
4. Click the *Sudo* group, then select the **Rules** tab.
5. In the **Fact** field, enter `name`.
6. From the **Operator** drop-down list, select **~** (matches regex).
7. In the **Value** field, enter `.*`.
8. Click **Add rule**.

   This rule will ["dynamically" pin all nodes]((./console_classes_groups.html#adding-nodes-dynamically) to the **Sudo** group. Note that this rule is for testing purposes and that decisions about pinning nodes to groups in a production environment will vary from user to user.


**To add the** `sudo` **and** `privileges` **classes to the Sudo group**:

1. On the __Classification__ page, select the __Sudo__ group.

2. Click the __Classes__ tab.

3. In the __Class name__ field, begin typing `sudo`, and select it from the autocomplete list.

4. Click __Add class__.

5. Click __Commit 1 change__.

   **Note**: The `sudo` class now appears in the list of classes for the __Sudo__ group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

6. Repeat steps 4 - 6 to add the `privileges` class.

7. From the command line of your Puppet master, run `puppet agent -t`.

8. From the command line of each PE-managed node, run `puppet agent -t`.

   This will configure the nodes using the newly-assigned classes. Wait one or two minutes.

> Congratulations! You’ve just created the `privileges` class that you can use to define and enforce a sudoers configuration across your PE-managed infrastructure.

## Other resources

Check out some of the other guides in our PE QSG series:

- [NTP quick start guide](./quick_start_ntp.html)
- [SSH quick start guide](./quick_start_ssh.html)
- [DNS quick start guide](./quick_start_dns.html)

Puppet offers many opportunities for learning and training, from formal certification courses to guided online lessons. We've noted a few below; head over to the [learning Puppet page](https://puppetlabs.com/learn) to discover more.

* [Learning Puppet](/learning/) is a series of exercises on various core topics about deploying and using PE.
* The Puppet workshop contains a series of self-paced, online lessons that cover a variety of topics on Puppet basics. You can sign up at the [learning page](https://puppetlabs.com/learn).
