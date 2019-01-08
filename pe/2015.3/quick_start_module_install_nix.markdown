---
layout: default
title: "Module Installation Quick Start Guide"
canonical: "/pe/latest/quick_start_module_install_nix.html"
---

## Overview

In this guide, you'll install the puppetlabs-apache module, a Puppet Enterprise supported module. Modules contain [classes](/puppet/4.3/reference/lang_classes.html), which are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures and manages nodes.  While you can use any module available on the Forge, PE customers can take advantage of [supported modules](http://forge.puppetlabs.com/supported). These modules are designed to make common services easier and are tested and maintained by Puppet Labs.

In the [Module Writing Basics for Linux QSG](./quick_writing_nix.html) you'll learn more about modules, including customizing and writing your own modules on either [Windows](./quick_writing_windows.html) or [*nix](./quick_writing_nix.html) platforms.

> **Prerequisites**: This guide assumes you've already [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [*nix agent node](./quick_start_install_agents_nix.html).

## Installing a Forge Module

1. **On the Puppet master**, run `puppet module search apache`. This command searches for modules from the Puppet Forge with `apache` in their names or descriptions.

   Your search results will display:

        Searching http://forgeapi.puppetlabs.com ...
        NAME                  DESCRIPTION                           AUTHOR        KEYWORDS
        puppetlabs-apache     Puppet module for apache              @puppetlabs   apache


   To view detailed info about the module, see the [Apache module on the Forge](http://forge.puppetlabs.com/puppetlabs/apache).

2. To install the Apache module, run: `puppet module install puppetlabs-apache`. The result looks like this:

        Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
        Notice: Downloading from http://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        /etc/puppetlabs/code/environments/production/modules
        └── puppetlabs-apache (v1.1.1)

> You have just installed a Puppet module. All of the classes in the module are now available to be added to the console and assigned to nodes.

> ### A Quick Note about Module Directories
>
>By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. In a fresh installation, you need to create this modules subdirectory yourself by navigating to `/etc/puppetlabs/code/environments/production` and running `mkdir modules`.
>
>**Note**: PE creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. Don't modify anything in or add modules of your own to `/opt/puppetlabs/puppet/modules`. The `/etc/puppetlabs/staging-code/modules` directory is for file sync use only; if you are not using Code Manager or file sync, do not add code to this directory.





--------

Next: [Adding Classes to Puppet Agent Nodes (*nix)](./quick_start_adding_class_nix.html)



