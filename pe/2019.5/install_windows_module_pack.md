# Install the Windows module pack

These steps show you how to install the module pack locally, but you can also install it on the master and pluginsync pushes the module pack to all of your nodes.

## Procedure

1.  Open the Puppet command prompt. If you haven't opened the command line interface before, enter `Command Prompt Puppet` in your **Start Menu**, and click **Start Command Prompt with Puppet**.

2.  To list modules that you currently have installed, enter `puppet module list` in your **Command Prompt** window. If you're just getting started, you likely have no modules installed yet.

3.  Next, to install the `puppetlabs/windows` module pack, type `puppet module install puppetlabs/windows`.

    Notice that you get a nice output of everything that’s installed.

    ```
    C:\>puppet module install puppetlabs/windows
    Notice: Preparing to install into
    C:/ProgramData/PuppetLabs/code/environments/production/modules ...
    Notice: Downloading from https://forgeapi.puppetlabs.com ...
    Notice: Installing -- do not interrupt ...
    C:/ProgramData/PuppetLabs/code/environments/production/modules
    └─┬ puppetlabs-windows (v2.1.0)
    ├─┬ chocolatey-chocolatey (v1.2.0)
    │ └── badgerious-windows_env (v2.2.2)
    ├── puppet-download_file (v1.2.1)
    ├── puppet-iis (v1.4.1)
    ├── puppet-windowsfeature (v1.1.0)
    ├── puppetlabs-acl (v1.1.1)
    ├── puppetlabs-powershell (v1.0.5)
    ├── puppetlabs-reboot (v1.2.0)
    ├─┬ puppetlabs-registry (v1.1.2)
    │ └── puppetlabs-stdlib (v4.9.0)
    └── puppetlabs-wsus_client (v1.0.0)
    ```


## Results

