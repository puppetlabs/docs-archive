---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Adding and removing agentless nodes

Using the inventory, you can manage nodes, including devices such as network switches or firewalls, without installing the Puppet agent on them. The inventory stores node and device information securely.

The inventory connects to agentless nodes through SSH or WinRM remote connections. The inventory uses transport definitions from installed device modules to connect to devices that can't have an agent installed on them.

After you add credentials to the inventory, authorized users can run tasks on these nodes and devices without re-entering credentials. On the **Tasks** page, these nodes and devices appear in the same list of targets as those that have agents installed.

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

## Add devices to the inventory

If you have installed modules for device transports in your production environment, you can add connections to those devices to your inventory. This lets you manage network devices such as switches and firewalls, and run Puppet and task jobs on them, just like other agentless nodes in your infrastructure.

### Before you begin

Make sure you have the permission **Nodes: Add and delete connection information from inventory service**.

### About this task

The connection details differ for each type of device transport, as defined in the module; see the device module's README for details.

CAUTION:

This initial implementation of network device configuration management is limited in how many device connections it can handle. Managing more than 100 devices can cause performance issues on the master. We plan to gather feedback from this release and improve the scaling and performance capabilities. In the meantime, avoid impacting master performance by limiting the number of network device connections you make to 100.

### Procedure

1.  In the console, click **Inventory**.

2.  Select the node type **Network devices**.

3.  Select a device type from the list of device transports that you have installed as modules in your production environment.

4.  Enter the device certname and other connection details, as defined in the transport module. Mandatory fields are marked with an asterisk. See the module README file if you need more details or examples specific to the transport.

5.  Click **Add node**.


### Results

After devices have been added to the inventory, they are added to PuppetDB, and you can view them from the **Nodes** page. Devices in the inventory can be added to an inventory node list when you set up a job to run tasks. To review each inventory device's connection options, or to remove the device from inventory, go to the **Connections** tab on the device's node details page.

## Remove agentless nodes and devices from the inventory

Remove an agentless node or device connection from the inventory from the **Connections** tab on its details page.

### Before you begin

Make sure you have the permission **Nodes: Add and delete connection information from inventory service**.

### About this task

### Procedure

1.  On the **Overview** or **Nodes** page, find the node or device whose connection you want to remove, and click its name to open its details page.

2.  Click **Connections**.

3.  Click **Remove connection**. The exact name of the link varies depending on the connection type: **Remove SSH Connection**, **Remove WinRM connection**, or similar.

4.  Confirm that you want to remove the connection.


### Results

When you remove a node or device connection from the inventory, PuppetDB marks it as expired after the standard node time-to-live \(`node-ttl`\) and then purges the node when it reaches its node-purge time-to-live limit \(`node-purge-ttl`\). At this point the node no longer appears in the console, and the node's license is available for use.

**Tip:** For more information about `node-ttl` and `node-purge-ttl` settings, see the PE docs for [database settings](https://puppet.com/docs/puppetdb/latest/configure.html#database-settings).

**Related information**  


[Node inventory API](node_inventory_api.md#)

