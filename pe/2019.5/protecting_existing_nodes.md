---
author: melissa amos <melissa.amos@puppet.com\>
---

# Protecting existing nodes

In brownfield environments – those in which you already have machines installed that PXE boot against the Razor server – you must take extra precautions to protect existing nodes. Failure to adequately protect existing nodes can result in data loss.

For recommended provisioning workflows in an existing environment, see [Provisioning for advanced users](provisioning_a_nix_node.md#).

## Protecting new nodes

By default, Razor marks all newly discovered nodes as installed, which prevents modifications to the node. This default is controlled with the `protect_new_nodes` class parameter of the **pe\_razor** class.

With `protect_new_nodes` enabled, Razor considers installed nodes eligible for reinstallation only when the installed flag is removed from the node using the `reinstall-node` command.

With `protect_new_nodes` disabled, Razor considers any nodes it detects – including installed nodes – eligible for provisioning. You might choose to disable `protect_new_nodes` if:

-   You’re sure all nodes in your environment need to be provisioned or reprovisioned.

-   You’ve manually registered existing nodes that you want to protect.


The `protect_new_nodes` option is specified as a class parameter of the **pe\_razor** class.

**Related information**  


[Register existing nodes manually](provisioning_a_nix_node.md#)

[Reinstall the node](provisioning_a_windows_node.md#)

[Change the protect\_new\_nodes default](provisioning_a_nix_node.md#)

## Registering nodes

To identify existing nodes to the Razor server – and prevent reprovisioning – you can manually register nodes using the `register-node` command. The `register-node` command identifies a node as installed, which signals Razor to ignore the node.

To successfully register nodes, you must provide enough `hw-info` details for Razor to identify the nodes when they’re detected.

**Related information**  


[Register existing nodes manually](provisioning_a_nix_node.md#)

## Limiting the number of nodes a policy can bind to

You can use the `max_count` attribute for policies to limit the number of slots available for provisioning.

For example, at initial installation, no slots are available, so no machines are provisioned. At this point, you can examine your resource pool or mark specific nodes as registered. If you create a new policy with a value of 1for `max_count`, there’s now one slot available for provisioning. The first qualified node that checks in binds to the policy while all other nodes remain unprovisioned.

