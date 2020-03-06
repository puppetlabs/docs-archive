# Start installing \*nix agents

A common way to manage nodes in PE is to install an agent on them.

In this guide you install a \*nixPuppet agent and how to add agentless nodes to your inventory.

These instructions assume you've installed PE.

## Install agents from the console

You can use the console to leverage tasks that install \*nix or Windows agents on target nodes.

### Before you begin

Add classes to the **PE Master** node group for each agent platform used in your environment. For example, `pe_repo::platform::el_7_x86_64`.

Make sure you have permission to run the appropriate task to install agents on all nodes:

-   \*nix targets use the task pe\_bootstrap::linux

-   Windows targets use the task pe\_bootstrap::windows


For Windows targets, this task requires:

-   Windows 2008 SP2 or newer
-   PowerShell version 3 or higher
-   Microsoft .NET Framework 4.5 or higher

### About this task

### Procedure

1.  In the console, click **Inventory**.

2.  Select the node type **Nodes with Puppet agents**.

3.  Select a transport method.

    -   **SSH** for \*nix targets
    -   **WinRM** for Windows targets
4.  Enter the target host names and the credentials required to access them. If you use an SSH key, include begin and end tags.

5.  Click **Add nodes**.

    **Tip:** Click **Installation job started** to view the job details for the task.

    Agents are installed on the target nodes and then automatically submit certificate signing requests \(CSR\) to the master. The list of unsigned certificates is updated with new targets.


## Add agentless nodes to the inventory

Add nodes that don't have the Puppet agent installed to the inventory so you can run tasks on them.

### Before you begin

Add classes to the **PE Master** node group for each agent platform used in your environment. For example, `pe_repo::platform::el_7_x86_64`.

Make sure you have the permission **Nodes: Add and delete connection information from inventory service**.

### Procedure

1.  In the console, click **Inventory**.

2.  Select the node type **Nodes without Puppet agents**.

3.  Select a transport method.

    -   **SSH** for \*nix targets
    -   **WinRM** for Windows targets
4.  Enter target host names and the credentials required to access them. If you use an SSH key, include begin and end tags.

5.  Select additional target options. For example, to add a target port number, select **Target Port** from the drop-down list, enter the number, and click **Add**. For details, see [Transport configuration options](adding_and_removing_nodes_inventory.md#), below.

6.  Click **Add nodes**.


### Results

After the nodes have been added to the inventory, they are added to PuppetDB, and you can view them from the **Nodes** page. Nodes in the inventory can be added to an inventory node list when you set up a job to run tasks. To review each inventory node's connection options, or to remove the node from inventory, go to the **Connections** tab on the node's details page.

