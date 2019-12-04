---
layout: default
title: "Removing nodes"
canonical: "/pe/latest/node_deactivation.html"
---


[environment_override]: ./console_classes_groups_environment_override.html


## Remove nodes

To completely remove a node from PE, you must purge the node and revoke its certificate so that it doesn't continue to check in.

Removing a node:

* Deactivates the node in PuppetDB.
* Deletes the Puppet master's information cache for the node.
* Frees up the license that the node was using.
* Allows you to re-use the hostname for a new node.


1. On the agent node, stop the agent service.

   * Agent versions **4.0 or later**: `service puppet stop`
   * Agent versions **earlier than 4.0**,: `service pe-puppet stop`

   **Tip:** You can run `puppet --version` to see which version of Puppet you're using.

2. On the master, purge the node: `puppet node purge <CERTNAME>`

   The node's certificate is revoked, the certificate revocation list (CRL) is updated, and the node is deactivated in PuppetDB and removed from the console, increasing your license count. The node can't check in or re-register with PuppetDB on the next run.

3. If you have compile masters, run Puppet on them: `puppet agent -t`

   The updated CRL is managed by Puppet and distributed to compile masters.

4. (Optional) If the node you're removing was pinned to any node groups, manually unpin it from individual node groups or from all node groups using the [`unpin-from-all` command endpoint](./nc_commands.html#post-v1commandsunpin-from-all).

5. (Optional) If the node still exists but you no longer want to manage it, stop MCollective on the node:

   1. [Uninstall the Puppet agent](./install_uninstalling.html#uninstalling-pe-from-agent-nodes) or stop the mcollective service:

   * Agent versions **4.0 or later**: `service mcollective stop`
   * Agent versions **earlier than 4.0**: `service pe-mcollective stop`

   2. Remove the node's certificate in `/etc/puppetlabs/mcollective/ssl/clients`.

