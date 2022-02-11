---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Removing MCollective

Remove MCollective and its related files from the nodes in your infrastructure.

## About this task

**Note:** This procedure does not remove your MCollective or ActiveMQ log files.

## Procedure

1.  In the console, click **Classification**, and select the node group **PE Infrastructure**.

2.  On the **Configuration** tab, find the **puppet\_enterprise** class. Select the **mcollective** parameter and edit its value to absent.

    |Class|Parameter|Value|
    |-----|---------|-----|
    |`puppet_enterprise`|`mcollective`|`absent`|

3.  Click **Add parameter** and commit the change.

4.  Set up a job and run Puppet on the **PE Agent \(production\)** node group to enforce your changes.


## Results

The server components of MCollective, including pe-activemq and the peadmin user, are removed from the master and the MCollective service on agents is stopped. You must upgrade to 2019.0 or later to completely remove MCollective from agents.

**Related information**  


[Move from MCollective to Puppet orchestrator](migrating_from_mcollective_to_orchestrator.md#)

