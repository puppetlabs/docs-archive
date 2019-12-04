---
layout: default
title: "Policies"
canonical: "/pe/latest/razor_policies.html"

---

Policies tell Razor what bits to install, where to get the bits, how they should be configured, and how the installed node can communicate with PE.

Policies can contain tags, which match characteristics of nodes to specific policies. For example, you might create a policy that installs a certain operating system on nodes greater than 5 GB in memory.

Policies without tags bind to any node. You might create policies without tags if:

* You have a single policy that installs the same operating system on all nodes.
* You want to install a specific number of various operating systems on a number of undifferentiated nodes. In this case, you can use the `max-count` attribute to specify how many of each operating system to provision.


## Example

To create a policy, *centos-for-small*, that is applied to the first 20 nodes that match the `small` tag:

~~~ bash
razor create-policy --name centos-for-small
	--repo centos-6.7 --broker pe --tag small
	--hostname 'host${id}.example.com'
	--root-password secret --max-count 20
~~~


## How policies bind to nodes

When a node boots into the Razor microkernel, it sends its facts to the Razor server. The node then binds to the first policy in the policy table that applies to the node. When a node binds to a policy, the node is provisioned with the operating system specified by the policy. If the node doesn't match any policies, it continues to send facts periodically to the Razor server and remains unprovisioned until it does match a policy.

Policies don't bind to nodes if:

* The policy is disabled.
* The policy has already reached the maximum number of nodes that can bind to it.
* The policy requires tags that don't apply to the node.

>**Important:** If you don't manage policies carefully, you can inadvertently enable Razor to match with and provision machines that you don't want to provision. In the case of existing servers, this can lead to catastrophic data loss. See [Protecting existing nodes](./razor_brownfield.html) for strategies on provisioning in a brownfield environment.


## The policy table

Policies are stored in a policy table. The order of the policy table is important because Razor applies the first policy that applies to a node.

You can influence the order of policies by:

+ Using the `create-policy` command with `before` or `after` parameters to indicate where the new policy should appear in the policy table.
+ Using the `move-policy` command with `before` and `after` parameters to reorder existing policies.


## Best practices for scalable policy matching

You can speed provisioning and reduce load on the Razor server by limiting the number of tags used to match policies.

If you want to assign specific policies to specific nodes in a granular fashion, try one of these strategies:

* Create tags using the `has_macaddress` operator to identify a list of nodes to be assigned the tag, for example, `["has_macaddress", "de:ea:db:ee:f0:00", "de:ea:db:ee:f0:01"]`.
* Set the `policy` metadata key on nodes using the [`modify-node-metadata`](./razor_client_commands.html#modify-node-metadata) command, then create matching tags, for example, `["=", ["metadata", "policy"], "policy1"]`.



**Related links**:

* [Create a policy](./razor_using.html#create-a-policy)
* [Policy client commands](./razor_client_commands.html#policy-commands)
* [Policy APIs](./razor_reference.html#policies)

* * *

