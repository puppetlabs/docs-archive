# Start installing modules

Modules are shareable, reusable units of Puppet code that extend Puppet across your infrastructure by automating tasks such as setting up a database, web server, or mail server. In this section, you install a module from the Puppet Forge.

The Puppet Forge is a repository for modules created by Puppet and the Puppet community. It contains thousands of modules submitted by users and Puppet developers for you to use. In addition, PE customers can take advantage of supported modules, which are designed to make common services easier and are tested and maintained by Puppet.

In this section, you install the ``puppetlabs-wsus_client` module`, a Puppet Enterprise supported module. In a subsequent section, [Writing modules for Windows](module_writing_windows_getting_started.md#), you learn more about modules, including how to write your own.

The process for installing a module is the same on both Windows and \*nix operating systems.

These instructions assume you've installed PE and at least one Windows agent node.

**Parent topic:**[Install and administer Puppet Enterprise on Windows](install_administer_pe_windows.md)

## Step 1: Create the modules directory

By default, Puppet keeps modules in `C:\ProgramData\PuppetLabs\code environments\production\modules` or, on \*nix /`etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. In a fresh installation, you need to create this modules subdirectory yourself.

**Note:** PE creates two other module directories: `/opt/puppetlabs/puppet/modules` and `/etc/puppetlabs/staging-code/modules`. Don't modify anything in or add modules of your own to `/opt/puppetlabs/puppet/modules`. The `/etc/puppetlabs/staging-code/modules` directory is for file sync use only; if you are not using Code Manager or file sync, do not add code to this directory.

1.  Navigate to the `production` directory and run: `mkdir modules`


## Step 2: Install a Forge module

The `wsus_client` module, or Windows Server Update Service \(WSUS\) module, lets Windows administrators manage operating system updates using their own servers instead of Microsoft's Windows Update servers.

1.  Run `puppet module install puppetlabs-wsus_client`

    If you're installing a module directly on your Windows machine, the output looks like this:

    ```
    PS C:\Users\Administrator> puppet module install puppetlabs-wsus_client
    Notice: Preparing to install into 
    C:/ProgramData/PuppetLabs/code/environments/production/modules ...
    Notice: Downloading from https://forgeapi.puppetlabs.com ...
    Notice: Installing -- do not interrupt ...
    C:/ProgramData/PuppetLabs/code/environments/production/modules
    +-- puppetlabs-wsus_client (v1.0.1)
      +-- puppetlabs-registry (v1.1.3)
      +-- puppetlabs-stdlib (v4.11.0)
    ```

    This is the output \(on a \*nix machine\):

    ```
    Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
    Notice: Downloading from http://forgeapi.puppetlabs.com ...
    Notice: Installing -- do not interrupt ...
    /etc/puppetlabs/code/environments/production/modules
    └─┬ puppetlabs-wsus_client (v1.0.1)
    ├── puppetlabs-registry (v1.1.3)
    └── puppetlabs-stdlib (v4.11.0)
    ```


You have just installed a module. You can read about this module in the [module readme](https://forge.puppet.com/puppetlabs/wsus_client/readme). All of the classes in the module are now available to be added to the console and assigned to nodes.

Next: Adding classes to Windows agent nodes.

