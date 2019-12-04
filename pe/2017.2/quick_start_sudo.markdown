---
layout: default
title: "Sudo users quick start guide"
canonical: "/pe/latest/quick_start_sudo.html"
---

[downloads]: http://info.puppetlabs.com/download-pe.html
[sys_req]: ./sys_req_os.html
[agent_install]: ./install_agents.html
[install_overview]: ./install_basic.html

The Puppet Enterprise sudo users quick start guide provides instructions for getting started managing sudo privileges across your PE deployment, using a module from the Puppet Forge in conjunction with a simple module you will write.

In most cases, you want to manage sudo on your nodes to control which system users have access to elevated privileges. Using this guide, you will:

* [Install the `saz-sudo` module as the foundation for your management of sudo privileges](#install-the-saz-sudo-module).
* [Write a simple `privileges` module to manage a few resources that set privileges for certain users, which will be managed by the `saz-sudo` module](#write-the-privileges-module).
* [Create a Sudo node group](#create-the-sudo-node-group)
* [Add classes from the `privileges` and `sudo` modules to your agent nodes in the console](#add-the-privileges-and-sudo-classes).

Before you begin, you must have installed PE. Refer to the [installation overview][install_overview] and the [agent installation instructions][agent_install] for complete instructions. See the [supported operating system documentation][sys_req] for supported platforms. This guide assumes that you are *not* using Code Manager or r10k.

>**Tip**: Follow the instructions in the [NTP quick start guide](./quick_start_ntp.html) to have PE ensure time is in sync across your deployment.

## Install the saz-sudo module

To start, you'll install the `saz-sudo` module. This module, available on the Puppet Forge, is one of many modules written by a member of our user community. Learn more about the module by visiting [the Puppet Forge](http://forge.puppet.com/saz/sudo).

1. From the PE master, run `puppet module install saz-sudo`.

   You should see output similar to the following:

   ```
Preparing to install into /etc/puppetlabs/puppet/modules ...
Notice: Downloading from http://forgeapi.puppetlabs.com ...
Notice: Installing -- do not interrupt ...
/etc/puppetlabs/puppet/modules
└── saz-sudo (v2.3.6)
    └── puppetlabs-stdlib (3.2.2) [/opt/puppetlabs/puppet/modules]
   ```
   
That's it! You've just installed the saz-sudo module. You'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your agents.

## Write the `privileges` module

In this step, you'll write a simple module to manage sudo privileges. This module contains just one *class*. [Classes]({{puppet}}/lang_classes.html) are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures nodes.

Modules are directory trees. For this task, you'll create the following files:

 - `privileges/` (the module name)
   - `manifests/`
      - `init.pp` (contains the `privileges` class)

> #### About module directories
>
>By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules that you download from the Forge and those you write yourself.
>
>PE also creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. For this guide, don't modify or add anything to either of these directories.
>
>There are plenty of resources about modules and the creation of modules that you can reference. Check out [Puppet: Module fundamentals]({{puppet}}/modules_fundamentals.html), [Puppet: The modulepath]({{puppet}}/dirs_modulepath.html), the [Beginner's guide to modules](/guides/module_guides/bgtm.html), and the [Puppet Forge](https://forge.puppetlabs.com/).

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

That's it! You've written a module that contains a class that, when applied, ensures that your agent nodes have the correct sudo privileges set for the root user and the "admin" and "wheel" groups. You will add this class at the same time you add the `saz-sudo` module.

> #### About the resources in the `privileges` class
>
> * `user 'root'`: This resource ensures that the root user has a centrally defined password and shell. Puppet enforces this configuration and report on and remediate any drift detected, such as if a rogue admin logs in and changes the password on an agent node.
>
> * `sudo::conf 'admins'`: Create a sudoers rule to ensure that members of the admin group have the ability to run any command using sudo. This resource creates configuration fragment file to define this rule in `/etc/sudoers.d/`. It is usually called something like `10_admins`.
>
> * `sudo::conf 'wheel'`: Create a sudoers rule to ensure that members of the wheel group have the ability to run any command using sudo. This resource creates a configuration fragment to define this rule in `/etc/sudoers.d/`. It is usually called something like `10_wheel`.

## Create the Sudo node group

Next, you'll set up a **Sudo** node group that will include all your nodes.

1. In the PE console, click **Classification**, and click **Add group**.
2. Specify options for the new node group:
   - **Parent name** -- Select **All Nodes**.
   - **Group name** -- Enter a name that describes the role of this environment node group, for example, *Sudo*.
   - **Environment** -- Select **production**.
   - **Environment group** -- Don't select this option.
3. Click **Add**.
4. Click the *Sudo* group, and select the **Rules** tab.
5. In the **Fact** field, enter `name`.
6. From the **Operator** drop-down list, select **~** (matches regex).
7. In the **Value** field, enter `.*`.
8. Click **Add rule**.

   This rule ["dynamically" pins all nodes](./console_classes_groups.html#adding-nodes-dynamically) to the **Sudo** group. Note that this rule is for testing purposes and that decisions about pinning nodes to groups in a production environment will vary from user to user.

## Add the `privileges` and `sudo` classes

[classification_selector]: ./images/quick/classification_selector.png

The `privileges` module you wrote has only one class, but the `saz-sudo` module contains several classes. You'll add the `privileges` class that you wrote and the `sudo` class to the **Sudo** node group you created in the previous step. If you don't want to add these classes to all of your nodes, you can [pin the nodes "statically"](./console_classes_groups.html#adding-nodes-statically) or write a different rule to [add them "dynamically"](./console_classes_groups.html#adding-nodes-dynamically), depending on your needs.

1. In the console, click **Classification**, and find and select the __Sudo__ group.

2. On the **Classes** tab, in the __Class name__ field, enter `sudo`.

3. Click __Add class__, and commit changes.

   **Note**: The `sudo` class now appears in the list of classes for the __Sudo__ group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

4. Repeat steps 2 and 3 to add the `privileges` class.

5. From the command line of your Puppet master, run `puppet agent -t`.

6. From the command line of each PE-managed node, run `puppet agent -t`.

   This configures the nodes using the newly-assigned classes. Wait one or two minutes.

> Congratulations! You’ve just created the `privileges` class that you can use to define and enforce a sudoers configuration across your PE-managed infrastructure.

## Other resources

Check out some of the other guides in our PE QSG series:

- [NTP quick start guide](./quick_start_ntp.html)
- [SSH quick start guide](./quick_start_ssh.html)
- [DNS quick start guide](./quick_start_dns.html)

Puppet offers many opportunities for learning and training, from formal certification courses to guided online lessons. We've noted a few below; head over to the [learning Puppet page](https://puppetlabs.com/learn) to discover more.

* [Learning Puppet](/learning/) is a series of exercises on various core topics about deploying and using PE.
* The Puppet workshop contains a series of self-paced, online lessons that cover a variety of topics on Puppet basics. You can sign up at the [learning page](https://puppetlabs.com/learn).
