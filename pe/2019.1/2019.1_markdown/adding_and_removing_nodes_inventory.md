---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Adding and removing agentless nodes

Using the inventory, you can manage nodes without installing the Puppet agent on them. The inventory stores node information securely and makes nodes available in the console through SSH or WinRM remote connections.

After you add credentials to the inventory, authorized users can run tasks on these nodes without re-entering credentials. On the **Tasks** page, these nodes appear in the same list of targets as nodes that have agents installed.

## Add nodes to the inventory

Add nodes that don't have the Puppet agent installed to the inventory so you can run tasks on them.

### Before you begin

Make sure you have the permission **Nodes: Add and delete connection information from inventory service**.

### Procedure

1.  In the console, click **Inventory**.

2.  Select a transport method.

    -   **SSH** for \*nix targets
    -   **WinRM** for Windows targets
3.  Enter target host names and the credentials required to access them. If you use an SSH key, include begin and end tags.

4.  Select additional target options. For example, to add a target port number, select **Target Port** from the drop-down list, enter the number, and click **Add**. For details, see [Transport configuration options](adding_and_removing_nodes_inventory.md#), below.

5.  Click **Add nodes**.


### Results

After the nodes have been added to the inventory, they are added to PuppetDB, and you can view them from the **Nodes** page. Nodes in the inventory can be added to an inventory node list when you set up a job to run tasks. To review each inventory node's connection options, or to remove the node from inventory, go to the **Connections** tab on the node's details page.

## Transport configuration options

A list of transport configuration options for SSH and WinRM transports.

### Target options for SSH transport

|Option|Definition|
|------|----------|
|**Target port**|Connection port. Default is `22`.|
|**Connection time-out in seconds**|The length of time PE should wait when establishing connections.|
|**Run as another user**|After login, the user name to use for running commands.|
|**Temporary directory**|The directory to use when uploading temporary files to the target.|
|**Sudo password**|Password to use when changing users via `run-as`.|
|**Process request as tty**|Enable text terminal allocation.|

### Target options for WinRM transport

|Option|Definition|
|------|----------|
|**Target port**|Connection port. Default is `5986`, or `5985` if `ssl: false`|
|**Connection time-out in seconds**|The length of time PE should wait when establishing connections.|
|**Temporary directory**|The directory to use when uploading temporary files to the target.|
|**Acceptable file extension**|List of file extensions that are accepted for scripts or tasks. Scripts with these file extensions rely on the target node's file type association to run. For example, if Python is installed on the system, a `.py` script should run with `python.exe`. The extensions .`ps1`, `.rb`, and `.pp` are always allowed and run via hard-coded executables.|

## Remove nodes from the inventory

Remove a node from the inventory from the Connections tab on its details page.

### Before you begin

Make sure you have the permission **Nodes: Add and delete connection information from inventory service**.

### About this task

### Procedure

1.  On the **Overview** or **Nodes** page, find the node whose connection you want to remove, and click its name to open its details page.

2.  Click **Connections**.

3.  Click **Remove connection**. The exact name of the link varies depending on the node's connection type: **Remove SSH Connection**, **Remove WinRM connection**, or similar.

4.  Confirm that you want to remove the connection.


### Results

When you remove a node from the inventory, PuppetDB marks the node as expired after the standard node time-to-live \(`node-ttl`\) and then purges the node when it reaches its node-purge time-to-live limit \(`node-purge-ttl`\). At this point the node no longer appears in the console, and the node's license is freed up.

**Tip:** For more information about `node-ttl` and `node-purge-ttl` settings, see the PE docs for [database settings](https://puppet.com/docs/puppetdb/latest/configure.html#database-settings).

**Related information**  


[Node inventory API](node_inventory_api.md#)

