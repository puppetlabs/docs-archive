---
author: melissa amos <melissa.amos@puppet.com\>
---

# Policies

Policies tell Razor what bits to install, where to get the bits, how they should be configured, and how the installed node can communicate with Puppet Enterprise.

Policies can contain tags, which match characteristics of nodes to specific policies. For example, you might create a policy that installs a certain operating system on nodes greater than 5GB in memory.

Policies without tags bind to any node. You might create policies without tags if:

-   You have a single policy that installs the same operating system on all nodes.
-   You want to install a specific number of various operating systems on a number of undifferentiated nodes. In this case, you can use the `max-count` attribute to specify how many of each operating system to provision.

For example, to create a policy, *centos-for-small*, that is applied to the first 20 nodes that match the `small` tag:

```bash
razor create-policy --name centos-for-small
	--repo centos-6.7 --broker pe --tag small
	--hostname 'host${id}.example.com'
	--root-password secret --max-count 20
```

## How policies bind to nodes

When a node boots into the Razor microkernel, it sends its facts to the Razor server. The node then binds to the first policy in the policy table that applies to the node. When a node binds to a policy, the node is provisioned with the operating system specified by the policy.

If the node doesn't match any policies, it continues to send facts periodically to the Razor server and remains unprovisioned until it does match a policy.

Policies don't bind to nodes if:

-   The policy is disabled.
-   The policy has already reached the maximum number of nodes that can bind to it.
-   The policy requires tags that don't apply to the node.

**Important:** If you don't manage policies carefully, you can inadvertently enable Razor to match with and provision machines that you don't want to provision. In the case of existing servers, this can lead to catastrophic data loss.

## The policy table

Policies are stored in a policy table. The order of the policy table is important because Razor applies the first policy that matches to a node.

You can influence the order of policies by:

-   Using the `create-policy` command with `before` or `after` parameters to indicate where the new policy should appear in the policy table.
-   Using the `move-policy` command with `before` and `after` parameters to reorder existing policies.

**Related information**  


[Create a policy](provisioning_a_windows_node.md#)

[Policy commands](using_the_razor_client.md#)

[Policies API](api_reference.md#)

[Node metadata commands](using_the_razor_client.md#)

