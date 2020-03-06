# Installing tasks and plans

Puppet Enterprise comes with some pre-installed tasks, but you must install all plans or any other tasks you want to use.

**Note:** Many of the modules that come pre-installed with the open-source Bolt tool will not be available by default in PE.

PE comes with the following tasks already installed:

-   `package` - Inspect, install, upgrade, and manage packages.
-   `service` - Start, stop, restart, and check the status of a service.
-   `facter_task` - Inspect the value of system facts.
-   `puppet_conf` - Inspect Puppet agent configuration settings.

PE does not come with any pre-installed plans. You must download or write them.

Tasks and plans are packaged in Puppet modules.

Tasks can be installed from the [Forge](https://forge.puppet.com/) and managed with a Puppetfile and Code Manager.

Plans must be installed using Code Manager. See the section on code deployment for plans in the [Plan caveats and limitations](plans_limitations.md) doc.

If you plan to run tasks or plans from the console, they must be installed into the `production` environment.

Additional modules that include helpful content include:

-   [puppet\_agent](https://forge.puppet.com/puppetlabs/puppet_agent): Install Puppet Agent package.

-   [reboot](https://forge.puppet.com/puppetlabs/reboot): Manage system reboots.


**Related information**  


[Managing environment content with a Puppetfile](puppetfile.md#)

