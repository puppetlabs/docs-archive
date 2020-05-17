# Add a module

Modules are self-contained, shareable bundles of code and data. Each module manages a specific task in your infrastructure, like installing and configuring a piece of software. You can write your own modules or download pre-built modules from the Forge. While you can use any module available on the Forge, PE customers can take advantage of supported modules. These modules are designed to make common services easier, and are tested and maintained by Puppet. A lot of your infrastructure will be supported by modules, so it is important to learn how to install, build, and use them.

## Install the `apache` module

To get you started working with modules, install the `puppetlabs-apache` module, which automates installing, configuring, and managing Apache services.

### Before you begin

These instructions assume you've installed PE and at least one \*nix agent node.

### Procedure

1.  Go to the [apache module in the Forge](https://forge.puppet.com/puppetlabs/apache).

2.  Follow the instructions in the **r10k or Code Manager** drop down to add the module declaration to your Puppetfile.

    If you don’t specify options, Code Manager installs the latest version and does not update it automatically. To always have the latest version installed, specify `:latest` and it will update automatically when a new version is released. To install a specific version of the module that does not update automatically, specify the version number as a string.

    For example, to install version 5.3.0 of the Apache module that will not update, use the following code:

    ```
    mod 'puppetlabs-apache', '5.3.0'
    ```

3.  SSH into your master and deploy code using the `puppet-code deploy --all` command.


### Results

You have just installed a Puppet module!

## View the `apache` task in the console

When you successfully install a module, you can start running tasks that are included in the module from the console. The `puppetlabs/apache` module contains a task that allows you to perform apache service functions.

### About this task

To see the task in the console:

### Procedure

1.  In the **Run** section, under **Task**, type apache in the **Task** field.


### Results

You can now run the `apache` task.

If you don't see the `apache` task, try refreshing the console in the browser. If it still does not populate, double check the `puppetlabs/apache` module is in your Puppetfile and try again.

