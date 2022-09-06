---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Disabling MCollective

Turn off MCollective on a single node or on all nodes.

**Related information**  


[Move from MCollective to Puppet orchestrator](migrating_from_mcollective_to_orchestrator.md#)

[Removing MCollective](removing_mcollective.md)

## Disable MCollective on select nodes

Since the Puppet master server supports managing non- PE agent nodes \(including things like network devices\), you should disable MCollective on these nodes.

To disable MCollective for a node, in the console, create a rule in the `PE MCollective` group that excludes the node. This will prevent PE from attempting to enable MCollective on that node.

**Related information**  


[Add nodes to a node group](grouping_and_classifying_nodes.md#)

## Disable MCollective on all nodes

Turn off MCollective and stop `mco` commands from running on your system.

### About this task

### Procedure

1.  In the console, click **Classification**, and select the node group **PE Infrastructure**.

2.  On the **Configuration** tab, find the **puppet\_enterprise** class. Select the **mcollective** parameter and edit its value to stopped.

    |Class|Parameter|Value|
    |-----|---------|-----|
    |`puppet_enterprise`|`mcollective`|`stopped`|

3.  Click **Add parameter** and commit the change.

4.  Set up a job and run Puppet on the **PE Agent \(production\)** node group to enforce your changes.


### Results

`mco` commands will no longer run on the nodes in your infrastructure.

