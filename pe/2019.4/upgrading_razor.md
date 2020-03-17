---
author: melissa amos <melissa.amos@puppet.com\>
---

# Upgrading Razor

If you used Razor in a previous Puppet Enterprise environment, upgrade Razor to keep your Puppet Enterprise and Razor versions synched.

After a Puppet Enterprise upgrade, the `pe_razor` class continues to operate normally, but we recommend upgrading Razor as soon as possible to avoid unintended effects.

**Note:** If nodes are actively provisioning during upgrade, provisioning might fail. You can resume provisioning after `pe-razor-server` restarts.

**Parent topic:**[Provisioning with Razor](provisioning_with_razor.md)

## Upgrade Razor from Puppet Enterprise 2015.2.x or later

Upgrading from 2015.2.x or later is a mostly automated process that replaces the software repository, installs software packages, and migrates the Razor database. Upgrading to 2016.2 or later requires manual migration of any custom configuration from your config.yaml file to parameters in the `pe_razor` class.

If you’re upgrading to Puppet Enterprise 2016.2 or later and you’ve modified your `config.yaml` file – for example, by changing `protect_new_nodes` or customizing tasks, brokers, or hooks – make a note of the modified settings.

For instructions on upgrading Razor from Puppet Enterprise 3.8, see earlier versions of the Razor documentation. 

1.  Upgrade the master.

2.  Upgrade the agent on the Razor server node.

    The `pe-razor-server` service automatically restarts.

3.  \(Optional\) If you’re upgrading to 2016.2 or later, transfer any customized configurations to parameters in the `pe_razor` class.

    **Note:** To prevent accidentally overwriting machines during upgrade, the default for `protect_new_nodes` was changed to `true` in Puppet Enterprise 2016.2 and later. If your environment and workflows rely on provisioning all new nodes, you must manually change `protect_new_nodes` to `false` after upgrade, then run puppet and restart the `pe-razor-server` service.


**Tip:** You can run `razor --version` to verify that the upgrade was successful.

**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

