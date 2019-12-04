---
layout: default
title: "Protecting existing nodes"
canonical: "/pe/latest/razor_brownfield.html"

---

In *Brownfield environments* -- those in which you already have machines installed that PXE boot against the Razor server -- you must take extra precautions to protect existing nodes. Failure to adequately protect existing nodes can result in data loss.

For recommended provisioning workflows in an existing environment, see [Provisioning for advanced users](./razor_using.html#provisioning-for-advanced-users).

## Protect new nodes

By default, Razor marks all newly discovered nodes as `installed`, which prevents modifications to the node. This default is controlled with the `protect_new_nodes` class parameter of the `pe_razor` class.

With `protect_new_nodes` enabled, Razor considers installed nodes eligible for reinstallation only when the `installed` flag is removed from the node using the `reinstall-node` command.

With `protect_new_nodes` disabled, Razor considers any nodes it detects -- including installed nodes -- eligible for provisioning. You might choose to disable `protect_new_nodes` if:

* You're sure all nodes in your environment should be provisioned -- or reprovisioned.
* You've manually [registered existing nodes](#register-nodes) that you want to protect.

The `protect_new_nodes` option is specified as a class parameter of the `pe_razor` class.

**Related links**:

* [Reinstall node](./razor_using.html#reinstall-node)
* [Change the `protect_new_nodes` default](./razor_using.html#change-the-protect-new-node-default)


## Register nodes

To identify existing nodes to the Razor server -- and prevent reprovisioning -- you can manually register nodes using the `register-node` command. The `register-node` command identifies a node as `installed`, which signals Razor to ignore the node.

To successfully register nodes, you must provide enough `hw-info` details for Razor to identify the nodes when they're detected.

**Related links**:

* [Register existing nodes manually](./razor_using.html#register-existing-nodes-manually)


## Limit the number of nodes a policy can bind to

You can use the `max_count` attribute for policies to limit the number of slots available for provisioning. For example, at initial installation, no slots are available, so no machines are provisioned. At this point, you can examine your resource pool or mark specific nodes as registered. If you create a new policy with a value of `1` for `max_count`, there's now one slot available for provisioning. The first qualified node that checks in binds to the policy while all other nodes remain unprovisioned.

* * *
