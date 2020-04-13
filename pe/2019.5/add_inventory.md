# Add nodes to the inventory

Your inventory is the list of nodes managed by Puppet. Add nodes with agents, agentless nodes that connect over SSH or WinRM, or add network devices like network switches and firewalls. Agent nodes help keep your infrastructure in your desired state. Agentless nodes do not have an agent installed, but can do things like run tasks and plans.

## Add agent nodes

Use the console to add agent nodes to the inventory. Agents help with configuration management by periodically correcting changes to resources and reporting information to the master about your infrastructure.

### About this task

### Procedure

1.  In the console, click **Inventory**.

2.  Click **Install agents**.

3.  Select the transport method. This connection is used to remotely install the agent on the target node.

    -   **SSH** for \*nix targets
    -   **WinRM** for Windows targets
4.  Enter the target host names and the credentials required to access them. If you use an SSH key, include begin and end tags.

5.  Click **Add nodes**.

    **Tip:** Click **Installation job started** to view the job details for the task.

    Agents are installed on the target nodes and then automatically submit certificate signing requests \(CSR\) to the master. The list of unsigned certificates is updated with new targets.


## Add agentless nodes

Add nodes that will not or cannot have an agent installed on them. Agentless automation allows you to do things like update a package or restart a server on demand for node targets that don't have software installed.

### About this task

### Procedure

1.  In the console, click **Inventory**.

2.  Click **Connect over SSH or WinRM**.

3.  Select a transport method.

    -   **SSH** for \*nix targets
    -   **WinRM** for Windows targets
4.  Enter target host names and the credentials required to access them. If you use an SSH key, include begin and end tags.

5.  Select additional target options. For example, to add a target port number, select **Target Port** from the drop-down list, enter the number, and click **Add**. For details, see [Transport configuration options](adding_and_removing_nodes_inventory.md#).

6.  Click **Add nodes**.


### Results

After the nodes have been added to the inventory, they are added to PuppetDB, and you can view them from the **Nodes** page. Nodes in the inventory can be added to an inventory node list when you set up a job to run tasks. To review each inventory node's connection options, or to remove the node from inventory, go to the **Connections** tab on the node's details page.

## Managing certificate signing requests in the console

A certificate signing request appears in the console on the **Unsigned certs** page after you add an agent node to inventory. Accept or reject submitted requests individually or in a batch.

-   To manage requests individually, click **Accept** or **Reject**.
-   To manage the entire list of requests, click **Accept All** or **Reject All**. Nodes are processed in batches. If you close the browser window or navigate to another website while processing is in progress, only the current batch is processed.

After you accept the certificate signing request, the node appears in the console after the next Puppet run. To make a node available immediately after you approve the request, run Puppet on demand.

