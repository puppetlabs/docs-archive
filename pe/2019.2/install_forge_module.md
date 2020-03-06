# Install the Apache module

The Puppet Forge contains thousands of modules that you can use in your own environment.

## Before you begin

These instructions assume you've installed PE, at least one \*nix agent node.

## About this task

Modules are self-contained, shareable bundles of code and data. Each module manages a specific task in your infrastructure, like installing and configuring a piece of software. You can write your own modules or download pre-built modules from the Forge. While you can use any module available on the Forge, PE customers can take advantage of supported modules. These modules are designed to make common services easier, and are tested and maintained by Puppet. A lot of your infrastructure will be supported by modules, so it is important to learn how to install, build, and use them.

In this guide, you will install the pre-built `puppetlabs-apache`, which will help you install, configure, and manage Apache services.

## Procedure

1.  Install the Apache module by running `puppet module install puppetlabs-apache`.

    The results look like this:

    ```
    Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
        Notice: Downloading from http://forgeapi.puppetlabs.com ...
        Notice: Installing -- do not interrupt ...
        /etc/puppetlabs/code/environments/production/modules
        └── puppetlabs-apache (v1.1.1)
    ```

2.  Visit the Puppet Forge to learn more about the [apache module](https://forge.puppet.com/puppetlabs/apache).


## Results

You have just installed a Puppet module!

Next, use the `apache` class included in the `puppetlabs-apache` module to manage Apache with Puppet Enterprise.

## About module directories

By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules that you download from the Forge and those you write yourself.

PE also creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. For this guide, don't modify or add anything to either of these directories.

There are some resources about modules and the creation of modules that you can reference.

-   [Welcome to Puppet modules](https://puppet.com/docs/puppet/latest/modules.html)
-   [Module fundamentals](https://puppet.com/docs/puppet/latest/modules_fundamentals.html)
-   [The Forge](https://forge.puppet.com/)

