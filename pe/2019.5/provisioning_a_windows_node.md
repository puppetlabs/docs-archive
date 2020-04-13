---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Provisioning a Windows node

Provisioning deploys and installs your chosen operating system to target nodes.

## What triggers provisioning

There are four requirements for Razor to provision a node.

-   The node must boot with iPXE software.

-   The node's network must link to the Razor server through TFTP.

-   A Razor policy must match the node.

-   The node's `installed` flag must be set to `false`.


When these conditions are met, Razor recognizes the node, applies the first matching policy in the policy table, and provisions the node.

With these requirements in mind, you can modify your Razor workflow to suit your goals, your environment, and your familiarity with Razor.

-   [Provision for new users](provisioning_a_nix_node.md#) enables you to learn about Razor and verify tags before provisioning nodes.

-   [Provision for advanced users](provisioning_a_nix_node.md#) enables you to seamlessly provision nodes in an existing environment.


## Provision a Windows node

This Windows workflow adapts the provisioning for new users workflow. This process enables you to learn about Razor and verify tags before provisioning nodes.

### Before you begin

You're ready to provision a node after you configure:

-   A DHCP/DNS/TFTP service with SELinux configured to enable PXE boot

-   Puppet Enterprise

-   The Razor server and client


To provision Windows machines, you also need:

-   A Windows machine running the same OS that you plan to provision. This machine is used to create a WinPE image.

-   \(Optional\) An activation key for the OS. A trial license is used if you don't have an activation key.


To follow along with the examples in this workflow, you must also have a new node with at least 8GB of memory. Don’t boot the node before you begin the provisioning process.

### About this task

In this workflow, you load iPXE software and register nodes with the microkernel so you can view node details. Then you configure Razor objects, finishing with creating a policy. Provisioning is triggered when you reinstall the node in order to remove the `installed` flag.

The examples in this workflow demonstrate provisioning a sample node with Windows. You can modify the settings and scale up your workflow as needed for your environment.

### Configure SMB share

Because neither the WinPE environment nor the Windows installer can use an HTTP source for installation, you must use a server message block \(SMB\) server to store the Razor repositories.

#### About this task

You can configure SMB share automatically, using a Razor class parameter, or manually.

#### Automatically configure SMB share

Enable the SMB share class parameter on the Razor server to let Razor set up the SMB share automatically.

##### About this task

##### Procedure

1.  In the console, select **Nodes** \> **Classification**, then click the Razor server node group.

2.  On the **Configuration** tab, enable the SMB share parameter.

    |**Parameter**|**Value**|
    |-------------|---------|
    |**enable\_windows\_smb**|true|

    **Note:** If you change **enable\_windows\_smb** from **true** to **false** later, the share remains enabled but isn’t managed by Puppet.

3.  Commit changes, run Puppet, and then restart the `pe-razor-server` service.


##### Results

**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

#### Manually configure SMB share

If Samba is already installed on your Razor server, you can manually configure it to work with Razor.

##### About this task

##### Procedure

1.  Navigate to the Samba directory: `cd /etc/samba`

2.  Edit the `smb.conf` file:

    1.  Modify the network settings as necessary for your environment.

    2.  Edit the `global` service definition to [allow unauthenticated access](https://www.samba.org/samba/docs/man/manpages/smb.conf.5.html#idp60894080).

        ```
        [global]
            security     = user
            map to guest = bad user
        ```

    3.  Add a service definition that allows anonymous access and points to the`repo_store_root` class parameter of the `pe_razor` module.

        ```
        [razor]
            comment   = Windows Installers
            path      = /opt/puppetlabs/server/data/razor-server/repo
            guest ok  = yes
            writable  = no
            browsable = yes
        ```

3.  Restart Samba: `service smb restart`


##### Results

### Load iPXE software

Set your machines to PXE boot so that Razor can interact with the node and provision the operating system. This process uses both the `undionly.kpxe` file from the iPXE open source software stack and a Razor`bootstrap.ipxe` script.

#### About this task

#### Procedure

1.  Download the iPXE boot image [http://boot.ipxe.org/undionly.kpxe](http://boot.ipxe.org/undionly.kpxe) and copy the image to your TFTP server’s`/var/lib/tftpboot` directory:

    ```
    `cp undionly.kpxe /var/lib/tftpboot`
    ```

2.  Download the iPXE bootstrap script from the Razor server and copy the script to your TFTP server’s`/var/lib/tftpboot` directory:

    ```
    `wget   "https://$<RAZOR_HOSTNAME>:$<HTTPS_PORT>/api/microkernel/bootstrap?nic_max=1&http_port=$<HTTP_PORT>" -O /var/lib/tftpboot/bootstrap.ipxe`
    ```

    **Note:** Don’t use `localhost` as the name of the Razor host. The bootstrap script chain-loads the next iPXE script from the server, so it must contain the correct host name.


#### Results

### Register a node with the microkernel

Registering a node lets you learn about the node before Razor provisions it. With registered nodes, you can view facts about the node, add metadata to the node, and see which tags the node matches.

#### Procedure

1.  Boot the node. This can mean physically pressing the power button, using IPMI to manage the node’s power state, or, in the case of a VM, starting the VM.

    The node boots into the microkernel and Razor discovers the node. After the initial PXE boot, the node downloads the microkernel.

2.  On the Razor server, view the new node: `razor nodes`

    The output displays the new node ID and name, for example:

    ```
     id: "http://localhost:8150/api/collections/node/node1"
     name: "node1"
     spec: "/razor/v1/collections/nodes/member"
    ```

3.  View the node’s details: `razor nodes <NODE_NAME>`

    The output displays hardware and DHCP information, the path to the log, and a placeholder for tags, for example:

    ```
     hw_info:
             mac: ["08-00-27-8a-5e-5d"]
          serial: "0"
            uuid: "9a717dc3-2392-4853-89b9-27fec1aec7b2"
     dhcp_mac: "08-00-27-8a-5e-5d"
     log:
             log => http://localhost:8150/api/collections/node/node1/log
     tags: []
    ```

4.  When the microkernel is running on the machine, view facts about the node: `razor nodes <NODE_NAME> facts`

    Facter periodically sends facts back to the server about the node, including IP address, details about network cards, and block devices.


#### Results

### Build a WinPE image

Create a custom Windows Preinstallation Environment \(WinPE\) WIM image containing Razor scripts.

#### About this task

#### Procedure

1.  On an existing Windows machine running the same operating system that you plan to install, install the [Windows assessment and deployment kit](http://msdn.microsoft.com/en-us/library/windows/hardware/hh825486.aspx) in the default location.

2.  Copy the directory at `/opt/puppetlabs/server/apps/razor-server/share/razor-server/build-winpe` from the Razor server to the existing Windows machine. If the directory is absent from your Razor server, use [this archive](https://s3.amazonaws.com/pe-razor-resources/build-winpe.zip). 

3.  Change into the directory you created in the previous step, for example, `c:\build-winpe`

4.  \(Optional\) To include additional Windows drivers in the WIM image – for example, if your machines require proprietary UCS drivers – place `.inf` files in the `extra-drivers` folder inside the `build-winpe` directory.

    You can place individual files or directories in the `extra-drivers` folder.  

5.  Run one of these build scripts, depending on whether you’re using unsigned drivers:

    -   Standard build script 

        ```
        powershell -executionpolicy bypass -noninteractive -file build-razor-winpe.ps1 -razorurl http://razor:8150/svc
        ```

    -   Build script for unsigned drivers

        ```
        powershell -executionpolicy bypass -noninteractive -file build-razor-winpe.ps1 -razorurl http://razor:8150/svc -allowunsigned
        ```


#### Results

When the build script completes, a `razor-winpe.wim` image appears in a new `razor-winpe` directory inside the current working directory. 

### Create a repository and add the WinPE image

Because Razor can’t unpack Windows DVD images, you must create a stub repository and manually fill it with content.

#### About this task

#### Procedure

1.  Copy the Windows ISO image to the Razor server.

2.  Create an empty repository.

    For example:

    ```
    razor create-repo --name win2012r2 --task windows/2012r2
        --no-content true
    ```

3.  After the repository is created, log into your Razor server as root, change into the repository directory, `/opt/puppetlabs/server/data/razor-server/<REPO_NAME>`, then mount the ISO image:

    ```
     $ mount -o loop </PATH/TO/WINDOWS_SERVER.ISO> /mnt
     $ cp -pr /mnt/* <REPO_NAME>
     $ umount /mnt
     $ chown -R pe-razor: <REPO_NAME>
    ```

    **Note:** The repository directory is specified by the `repo_store_root` class parameter of the `pe_razor` module. Your directory might be different from the default if you customized this parameter.

4.  Copy the WinPE image from the existing Windows machine to the repository directory on the Razor server.


#### Results

**Related information**  


[Repositories](repositories.md)

[Repository commands](using_the_razor_client.md#)

[Repositories API](api_reference.md#)

### \(Optional\) Create tags

Tags let you group nodes based on their characteristics. You can then apply policies based on tags to install appropriate operating systems on tagged nodes. If you don’t specify tags for a policy, the policy binds to any node.

#### Procedure

1.  To create a tag called small with a rule that matches machines that have less than 4GB of memory:

    ```
     razor create-tag --name small
       --rule '["<", ["num", ["fact", "memorysize_mb"]], 4128]'
    ```

2.  \(Optional\) Inspect the tag on the server: `razor tags <TAG_NAME>`

    For example, `razor tags small` responds with:

    ```
     From https://razor:8151/api/collections/tags/small:
         name: small
         rule: ["<", ["num", ["fact", "memorysize_mb"]], 4128]
        nodes: 1
     policies: 0
    ```

3.  \(Optional\) Confirm that expected nodes now have the tag: `razor tags <TAG_NAME> nodes`

    For example, `razor tags small nodes` displays a table of registered nodes that have less than 4GB of memory.


#### Results

**Tip:** To see details about the policies associated with a tag, run `razor tags <TAG_NAME> policies`. To see its rule, run `razor tags <TAG_NAME> rule`.

**Related information**  


[Tags](tags.md)

[Tag commands](using_the_razor_client.md#)

[Tags API](api_reference.md#)

### \(Optional\) Create a broker

Brokers hand off nodes to configuration management systems like Puppet Enterprise.

#### About this task

#### Procedure

1.  To hand off Razor nodes to a Puppet master at `puppet-master.example.com`:

    ```
    razor create-broker --name pe --broker-type puppet-pe
      --configuration server=puppet-master.example.com
    ```


#### Results

**Related information**  


[Brokers](brokers.md#)

[Broker commands](using_the_razor_client.md#)

[Brokers API](api_reference.md#)

### Create a policy

Policies tell Razor what operating system to install on the provisioned node, where to get the OS software, how to configure it, and how to communicate between the node and Puppet Enterprise.

#### Procedure

1.  To create a policy that installs CentOS on machines tagged with `small`, then hands them off to Puppet Enterprise for management:

    ```
     razor create-policy --name centos-for-small
        --repo centos-6.7 --broker pe --tag small
        --hostname 'host${id}.example.com' --root-password secret
    ```


#### Results

**Note:** You can view details of a specific policy by running `razor policies <POLICY_NAME>`. You can view a table of all policies by running `razor policies`. The order in which policies are listed in the table is important because Razor applies the first matching policy to a node.

**Related information**  


[Policies](policies.md)

[Policy commands](using_the_razor_client.md#)

[Policies API](api_reference.md#)

### Reinstall the node

By default, Razor protects existing nodes from reprovisioning by marking all existing nodes as installed. You must specifically instruct the server to reinstall the node in order to trigger provisioning.

#### About this task

You can skip this step if you change the `protect_new_nodes` option to `false`, which allows Razor to provision a node as soon as it PXE boots with a matching policy. Be sure you understand how the `protect_new_nodes` option works before changing it, however. Failure to protect existing nodes can result in data loss.

#### Procedure

1.  Reinstall the node: `razor reinstall-node <NODE_NAME>`


#### Results

When you reinstall the node, Razor clears the `installed` flag and the node restarts and boots into the microkernel. The microkernel reports its facts, and Razor provisions the node by applying the first applicable policy in the policy table.

When provisioning is complete, you can log into the node using the `root_password` as specified by the node’s metadata, or by the policy that the node is bound to. You can also see the node and its details in the console, and manage it there as you would any other node.

## Viewing information about nodes

Use these commands to view details about nodes in your environment.

|Command|Result|
|-------|------|
|`razor nodes`|Displays a list of nodes that Razor knows about.|
|`razor nodes` `<NODE_NAME>`|Displays details about the specified node.|
|`razor nodes` `<NODE_NAME>` `log`|Displays a log that includes the timing and status of installation events, as well as downloads of kickstart files and post-install scripts.|

