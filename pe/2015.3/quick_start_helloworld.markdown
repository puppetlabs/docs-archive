---
layout: default
title: "Hello World! Quick Start Guide"
canonical: "/pe/latest/quick_start_helloworld.html"
---

## Overview

The following quick start guide introduces the essential components of Puppet module writing. In this guide, you will write a simple *nix-based module that contains two classes---one that will manage your message of the day (motd) and one that will create a notification on on the command line when you run Puppet.

While the module you'll write doesn't have an incredible amount of functionality, you'll learn the basic module directory structure and how to apply classes using the PE console. You'll encounter more complex module writing scenarios in other quick start guides.

## Write the `helloworld` Class

Some modules can be large, complex, and require a significant amount of trial and error. This module will be a very simple module to write. It contains just two classes.

> ### A Quick Note about Modules
>
>By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. In a fresh installation, you need to create this modules subdirectory yourself by navigating to `/etc/puppetlabs/code/environments/production` and running `mkdir modules`.
>
>**Note**: PE creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. Don't modify anything in or add modules of your own to `/opt/puppetlabs/puppet/modules`. The `/etc/puppetlabs/staging-code/modules` directory is for file sync use only; if you are not using Code Manager or file sync, do not add code to this directory. 

Modules are directory trees. For this task, you'll create the following files:

 - `helloworld/` (the module name)
   - `manifests/`
      - `init.pp` (contains the `helloworld` class)
      - `motd.pp` (contains a file resource that ensures the creation of the motd)

**To write the `helloworld` class**:

1. From the command line on the Puppet master, navigate to the modules directory:`cd /etc/puppetlabs/code/environments/production/modules`.
2. Run `mkdir -p helloworld/manifests` to create the new module directory and its manifests directory.
3. In the `manifests` directory, use your text editor to create the `init.pp` file, and edit it so that it contains the following Puppet code.

        class helloworld {
           notify { 'hello, world!': }
        }

4. Save and exit the file.
5. In the `manifests` directory, use your text editor to create the `motd.pp` file, and edit it so that it contains the following Puppet code.

        class helloworld::motd {
           file { '/etc/motd':
           owner  => 'root',
           group  => 'root',
           mode    => '0644',
           content => "hello, world!\n",
           }
        }

6. Save and exit the file.

> Hooray! You've written a module that contains two classes that will, once applied, show a notification message when Puppet runs, and manage the motd on your server---we'll take a closer at these actions after we add the classes. Please note that you'll need to wait a short time for the Puppet server to refresh before the classes are available to add to your nodes.

## Add the `helloworld` and `helloworld::motd` Classes in the Console

For this procedure, you're going to create a node group called **HelloWorld** and add the `helloworld` classes to it. Depending on your needs or infrastructure, you may have a different group that you'll assign `helloworld` to, but these same instructions would apply.

**To create the HelloWorld node group**:

1. In the console, click __Nodes__ in the side navigation bar, and select __Classification__.
2. In the **Node group name** field, name your group **HelloWorld**.

   **Note**: Leave the **Parent name** and **Environment** values as their defaults (**All Nodes** and **production**, respectively).

3. Click **Add group**.
4. On the __Classification__ page, click the __HelloWorld__ group, and click the __Rules__ tab.
5. In the **Fact** field, enter `name`.
6. From the **Operator** drop-down list, select **matches regex**.
7. In the **Value** field, enter `.*`.
8. Click **Add rule**.

   This rule will ["dynamically" pin all nodes](./console_classes_groups.html#adding-nodes-dynamically) to the **HelloWorld** group. Note that this rule is for testing purposes. Decisions about pinning nodes to groups in a production environment will vary.

**To add the** `helloworld` **classes to the HelloWorld group**:

1. On the __Classification__ page, click the __HelloWorld__ group.

2. Click the __Classes__ tab.

3. In the __Class name__ field, begin typing `helloworld`, and select it from the autocomplete list.

4. Click __Add class__.

5. Repeat steps 3 and 4 to add the `helloworld::motd` class.

5. Click the **Commit change** button.

   **Note**: The `helloworld` classes now appear in the list of classes for the __HelloWorld__ group, but they have not yet been configured on your nodes. For that to happen, kick off a Puppet run on each PE-managed node.

8. From the command line of each PE-managed node, run `puppet agent -t`.

   This will configure the nodes using the newly assigned classes.

### Viewing the Results

After you kick off the Puppet run, you should see the following on the command line as the `helloworld` class is applied:

    [root@master manifests]# puppet agent -t
    Info: Retrieving pluginfacts
    Info: Retrieving plugin
    Info: Loading facts
    Info: Caching catalog for master.example.com
    Info: Applying configuration version '1416331291'
    Notice: hello, world!
    Notice: /Stage[main]/Helloworld/Notify[hello, world!]/message: defined 'message' as 'hello, world!'
    Notice: Applied catalog in 9.42 seconds

Now run `cat /etc/motd`. The result shows: `hello, world!`

## Other Resources

>There are plenty of resources about modules and the creation of modules that you can reference. Check out [Puppet: Module Fundamentals](/puppet/4.3/reference/modules_fundamentals.html), [Puppet: The Modulepath](/puppet/4.3/reference/dirs_modulepath.html), the [Beginner's Guide to Modules](/guides/module_guides/bgtm.html), and the [Puppet Forge](https://forge.puppetlabs.com/).
>
> Check out the remainder of the [Quick Start Guide series](./quick_start.html) for additional module writing exercises.

---------
Next: [Installing Modules (*nix)](./quick_start_module_install_nix.html) or [Installing Modules (Windows)](./quick_start_module_install_windows.html)
