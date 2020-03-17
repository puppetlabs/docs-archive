# Installing tasks and plans

Puppet Enterprise comes with some pre-installed tasks, but you must install all plans or any other tasks you want to use.

**Note:** Many of the modules that come pre-installed with the open-source Bolt tool will not be available by default in PE.

PE comes with the following tasks already installed:

-   `package` - Inspect, install, upgrade, and manage packages.
-   `service` - Start, stop, restart, and check the status of a service.
-   `facter_task` - Inspect the value of system facts.
-   `puppet_conf` - Inspect Puppet agent configuration settings.

Tasks and plans are packaged in Puppet modules, which you can install directly from the [Forge](https://forge.puppet.com/) or install and manage them with a Puppetfile using Code Manager. See [Managing environment content with a Puppetfile](puppetfile.md#) for more information on declaring modules in your Puppetfile.

Additional modules that include helpful task and plan content include:

-   [puppet\_agent](https://forge.puppet.com/puppetlabs/puppet_agent): Install Puppet Agent package.

-   [reboot](https://forge.puppet.com/puppetlabs/reboot): Manage system reboots.


**Parent topic:**[Tasks and plans in PE](running_tasks_and_plans_pe.md)

