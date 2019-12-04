---
layout: default
title: "Module installation quick start guide"
canonical: "/pe/latest/quick_start_module_install_nix.html"
---

**Modules** are self-contained bundles of code and data. You can write your own modules ([more about that later](./quick_writing_nix.html)), and you can can download pre-built modules from the Puppet Forge. 

While you can use any module available on the Forge, PE customers can take advantage of [supported modules](http://forge.puppetlabs.com/supported). These modules are designed to make common services easier, and are tested and maintained by Puppet. In this quick start guide, you'll install the puppetlabs-apache module, a Puppet Enterprise-supported module. 

Before you begin, make sure you've [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [*nix agent node](./quick_start_install_agents_nix.html).

## Step 1: Install a Forge module

1. Install the Apache module by running: `puppet module install puppetlabs-apache`. 

   The result looks like this:

        Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
        Notice: Downloading from http://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        /etc/puppetlabs/code/environments/production/modules
        └── puppetlabs-apache (v1.1.1)

2. [Visit the Puppet Forge to learn more about the module.](http://forge.puppetlabs.com/puppetlabs/apache)

> You have just installed a Puppet module! Modules contain [classes]({{puppet}}/lang_classes.html), which are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures and manages nodes. 
> 
> Next, you'll use the `apache` class included in puppetlabs-apache to manage Apache on your agent node with Puppet Enterprise. [Click here when you're ready to get started.](./quick_start_adding_class_nix.html)  










