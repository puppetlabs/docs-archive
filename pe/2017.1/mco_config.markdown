---
layout: default
title: "Configuring MCollective"
---

Puppet Enterprise's MCollective can be configured to enable new actions, modify existing actions, restrict actions, and prevent run failures on non-PE nodes.

Disabling MCollective on some nodes
-----

By default, PE enables and configures MCollective on all agent nodes. This is generally desirable, but the Puppet code that manages this will not work on non-PE agent nodes, and will cause Puppet run failures on them.

Since the Puppet master server supports managing non-PE agent nodes (including things like network devices), you should disable MCollective on non-PE nodes.

To disable MCollective for a node, in the PE console, create a rule in the `PE MCollective` group that excludes the node. This will prevent PE from attempting to enable MCollective on that node. See [here][group] for instructions on including nodes in node groups in the console.

[group]: ./console_classes_groups.html#adding-nodes-to-a-node-group


Adding actions
-----

See the [Adding actions](./mco_adding_actions.html) page of this manual.

Changing the port used by MCollective/ActiveMQ
------

You can change the port that MCollective/ActiveMQ uses with a simple variable change in the console.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure**  group, select the **PE MCollective** group.
2. On the **Variables** tab, specify a variable:

   - __key__ -- Enter `fact_stomp_port`.

   - __value__ -- Enter the port number you want to use.

3. Click **Add variable**, and commit changes.

Configuring MCollective plugins
-----

Some MCollective agent plugins, including the [default set](./mco_actions.html)  included with Puppet Enterprise, have settings that can be configured.

Since the main configuration file is managed by PE, you must [put these settings in separate plugin config files](./mco_adding_actions.html#step-4-configure-the-plugin-optional).

Restricting MCollective actions
-----

See [Policy files][policy].

[policy]: mco_adding_actions.html#policy-files


Unsupported features
-----

### Adding new MCollective users and integrating applications

Adding new users is not supported in Puppet Enterprise. Future versions of PE may change MCollective's authentication backend, which will block additional users from working until they are updated to use the new backend. We plan to include an easy method to add new users in a future version of PE.

In the meantime, if you need to add a new user in order to integrate an application with PE, you can:

* Obtain client credentials and a config file as described in the [standard MCollective deployment guide][config_client].
* Write a Puppet module to distribute the new client's public key into the `${pe_mcollective::params::mco_etc}/ssl/clients/` directory. This class must use `include pe_mcollective` to ensure that the directory can be located.
* Assign that Puppet class to the `PE MCollective` group in the PE console.

Again, this process is _unsupported_ and may require additional work during a future upgrade.

[config_client]: /mcollective/deploy/standard.html#step-5-configure-clients



