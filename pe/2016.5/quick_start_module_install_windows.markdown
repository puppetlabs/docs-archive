---
layout: default
title: "Module installation quick start guide"
canonical: "/pe/latest/quick_start_module_install_windows.html"
---

### Overview

In this guide, you'll install the [puppetlabs-wsus_client module](https://forge.puppetlabs.com/puppetlabs/wsus_client), a Puppet Enterprise supported module. Modules contain [classes]({{puppet}}/lang_classes.html), which are named blocks of Puppet code and are the primary means by which Puppet Enterprise configures and manages nodes.

The [Puppet Forge](http://forge.puppetlabs.com) contains thousands of modules submitted by users and Puppet module developers that you can use. In addition, PE customers can take advantage of [supported modules](http://forge.puppetlabs.com/supported); these modules--designed to make common services easier--are tested and maintained by Puppet.

In [Module Writing Basics for Windows QSG](./quick_writing_windows.html) you'll learn more about modules, including how to write your own modules.

The process for installing a module is the same on both Windows and *nix operating systems.

> **Prerequisites**: This guide assumes you've already [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [Windows agent node](./quick_start_install_agents_windows.html).

### Installing a Forge module

1. To install the wsus_client module, run `puppet module install puppetlabs-wsus_client`. This is the output (on a *nix machine):

        Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
        Notice: Downloading from http://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        /etc/puppetlabs/code/environments/production/modules
        └─┬ puppetlabs-wsus_client (v1.0.1)
        ├── puppetlabs-registry (v1.1.3)
        └── puppetlabs-stdlib (v4.11.0)

    If you're installing a module directly on your Windows machine, the output looks like this:
    
        PS C:\Users\Administrator> puppet module install puppetlabs-wsus_client
        Notice: Preparing to install into 
        C:/ProgramData/PuppetLabs/code/environments/production/modules ...
        Notice: Downloading from https://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        C:/ProgramData/PuppetLabs/code/environments/production/modules
        +-- puppetlabs-wsus_client (v1.0.1)
          +-- puppetlabs-registry (v1.1.3)
          +-- puppetlabs-stdlib (v4.11.0)
        
You have just installed a Puppet module. All of the classes in the module are now available to be added to the console and assigned to nodes.

> #### Note about modules directories
>
>By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules` or from a Windows directory, `C:\ProgramData\PuppetLabs\code
environments\production\modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. In a fresh installation, you need to create this modules subdirectory yourself by navigating to `production` and running `mkdir modules`.
>
>**Note**: PE creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. Don't modify anything in or add modules of your own to `/opt/puppetlabs/puppet/modules`. The `/etc/puppetlabs/staging-code/modules` directory is for file sync use only; if you are not using Code Manager or file sync, do not add code to this directory.


--------

Next: [Adding classes to Puppet agent nodes (Windows)](./quick_start_adding_class_windows.html)

