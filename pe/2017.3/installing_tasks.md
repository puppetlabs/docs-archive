# Installing tasks

Puppet Enterprise comes with some pre-installed tasks, but you must install any other tasks you want to use.

PE comes with `facter_task,` `package`, `service`, and `puppet_conf`tasks. Use the `package` task to inspect, install, upgrade, and manage packages. Use the `service` task to start, stop, restart, and check the status of services running on your systems.

Tasks are packaged in Puppet modules, so you can install them directly from the Forge or install and manage them with a Puppetfile and Code Manager.

If you plan to run tasks from the console, those tasks must be installed into the `production` environment.

**Related topics**  


[Managing environment content with a Puppetfile](puppetfile.md#)

