---
layout: default
title: "Removing Nodes From PE"
canonical: "/pe/latest/node_deactivation.html"
---


[environment_override]: ./console_classes_groups_environment_override.html

## Removing Nodes From PE

To completely remove all traces of an agent node from your PE deployment, follow the steps described below. As an example, this workflow is useful when you have a cloud environment with ephemeral nodes that you are spinning up for a short time. The workflow achieves the following:

* Deactivates the node in PuppetDB.
* Deletes the Puppet master's information cache for the node.
* Frees up the license that the node was using.
* Allows you to re-use the hostname for a new node.

### Steps

1. **On the agent node**, stop the agent service:

   * For agent versions **4.0 or later**, run `service puppet stop`.
   * For agent versions **earlier than 4.0**, run `service pe-puppet stop`.

   > **Tip:** You can run `puppet --version` to see which version of Puppet you're using.

   This prevents the agent node from checking in with the Puppet master and PuppetDB before you restart Puppet server.

2. **On the Puppet master**, purge the node by running `puppet node purge <CERTNAME>`.

   This command revokes the certificate for the agent node and deactivates the node in PuppetDB. It also removes the agent node from the list of nodes in the PE console, and decreases the number of nodes that are being used under your PE license.

3. Still on the Puppet master, run `puppet agent -t` to kick off a Puppet run.

   This Puppet run will copy the certificate revocation list (CRL) to the correct SSL directory for delivery to the node.

4. Restart the Puppet master with `service pe-puppetserver restart`.

   This completely removes the agent's certificate from the certificate list. If you don't run `service pe-puppetserver restart`, the node will check in again on the next Puppet run and re-register with PuppetDB, which will increase the license count again.
   
   Alternatively, for this step, you can restart the Puppet server by [sending a HUP signal]({{puppetserver}}/restarting.html).

   > **Tip**: You will need to run `service pe-puppetserver restart`, or send a HUP signal, on any compile masters in your system.

   > **Note:** If a purged node still exists and still has PE installed, it can still check in but its Puppet run will fail.

   > **Note:** If you have nodes pinned to node groups in the node classifier, those nodes will still remain in the console as an artifact, even after they have been purged. You need to manually unpin these nodes from node groups to completely remove them from the console.

If your nodes will continue to exist, take the following two additional steps to stop MCollective from running on the node:

1. On the agent node, [uninstall the Puppet agent](./install_uninstalling.html#uninstalling-pe-from-agent-nodes) or stop the mcollective service:

   * For agent versions **4.0 or later**, run `service mcollective stop`.
   * For agent versions **earlier than 4.0**, run `service pe-mcollective stop`.

2. On the agent node, remove the node's certificate in `/etc/puppetlabs/mcollective/ssl/clients`.

