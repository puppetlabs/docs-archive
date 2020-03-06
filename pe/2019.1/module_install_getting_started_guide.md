# Start installing modules

Modules are self-contained bundles of code and data. You can write your own modules, and you can can download pre-built modules from the Puppet Forge. In this guide, you install a Puppet module, the primary means by which PE configures and manages nodes.

While you can use any module available on the Forge, PE customers can take advantage of supported modules. These modules are designed to make common services easier, and are tested and maintained by Puppet. In this getting started guide, you install the puppetlabs-apache module, a Puppet Enterprise-supported module.

These instructions assume you've installed PE and at least one \*nix agent node.

## Step 1: Install a Forge module

The Puppet Forge contains thousands of modules that you can use in your own environment.

### Procedure

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


### Results

You have just installed a Puppet module! Modules contain classes, which are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures and manages nodes.

Next, use the `apache` class included in `puppetlabs-apache` to manage Apache on your agent node with Puppet Enterprise.

