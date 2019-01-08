---
layout: default
title: "Module installation quick start guide"
canonical: "/pe/latest/quick_start_module_install_nix.html"
---

## Overview

In this guide, you'll install the puppetlabs-apache module, a Puppet Enterprise-supported module. Modules contain [classes]({{puppet}}/lang_classes.html), which are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures and manages nodes. While you can use any module available on the Forge, PE customers can take advantage of [supported modules](http://forge.puppetlabs.com/supported). These modules are designed to make common services easier, and are tested and maintained by Puppet.

In the module writing basics quick start guide, you'll learn more about modules, including customizing and writing your own modules on either the [Windows](./quick_writing_windows.html) or [*nix](./quick_writing_nix.html) platform.

> **Prerequisites**: This guide assumes you've already [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [*nix agent node](./quick_start_install_agents_nix.html).

## Installing a Forge module

To install the Apache module, run: `puppet module install puppetlabs-apache`. The result looks like this:

        Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
        Notice: Downloading from http://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        /etc/puppetlabs/code/environments/production/modules
        └── puppetlabs-apache (v1.1.1)

To view detailed info about the module, see the [Apache module on the Forge](http://forge.puppetlabs.com/puppetlabs/apache).

**You have just installed a Puppet module!** All of the classes in the module are now available to be added to the console and assigned to nodes.

> ### Note about module directories
>
>By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. 
>
>**Note**: PE also creates another module directory: `/opt/puppetlabs/puppet/modules`. Don't modify anything in or add modules of your own to this directory.

--------

Next: [Adding classes to Puppet agent nodes (*nix)](./quick_start_adding_class_nix.html)



