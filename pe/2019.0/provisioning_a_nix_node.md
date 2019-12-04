---
author: melissa amos <melissa.amos@puppet.com\>
---

# Provisioning a \*nix node

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


## Provision for new users

This workflow enables you to learn about Razor and verify tags before provisioning nodes.

### Before you begin

You're ready to provision a node after you configure:

-   A DHCP/DNS/TFTP service with SELinux configured to enable PXE boot

-   Puppet Enterprise

-   The Razor server and client


To follow along with the examples in these workflows, you must have a new node with at least 1GB \(2GB recommended\) of memory. Don’t boot the node before you begin the provisioning process.

### About this task

In this workflow, you load iPXE software and register nodes with the microkernel so you can view node details. Then you configure Razor objects, finishing with creating a policy. Provisioning is triggered when you reinstall the node in order to remove the `installed` flag.

The examples in this workflow demonstrate provisioning a sample node with CentOS 6.7. You can modify the settings and scale up your workflow as needed for your environment.

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

### Create a repository

Repositories contain – or point to – the operating system to install on a node.

#### About this task

You can create three types of repositories using specific attributes:

-   `url` – Points to content available on another server, for example, on a mirror that you maintain.

-   `iso-url` – Downloads and unpacks an ISO on the Razor server.

-   `no_content` – Creates a stub directory on the Razor server that you can manually fill with content.


#### Procedure

1.  To download a CentOS 6.7 ISO and create a repository from it:

    ```
    razor create-repo --name centos-6.7 --task centos
        --iso-url http://centos.sonn.com/6.7/isos/x86_64/CentOS-6.7-x86_64-bin-DVD1.iso
    
    ```


#### Results

The ISO is downloaded onto the Razor server, then extracted to the repository. This can take some time to complete. To monitor progress, you can run `razor commands` to view the task status or `ls -al /tmp` to see the downloaded file size.

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

### Add the `pe_repo` class to the **PE Master** node group

To manage a node handed off by the broker, the master must include a class that matches the node’s architecture.

#### About this task

**Note:** Skip this step if the node you’re provisioning has the same architecture as your master, or if the master already includes a **pe\_repo** class that matches the node’s architecture.

#### Procedure

1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

2.  On the **Configuration** tab in the **Add new class** field, select `pe_repo::platform::<VERSION>`.

    For example, `pe_repo::platform::el_7_x86_64`.

3.  Click **Add class** and then commit changes.

4.  On the master, run Puppet.


#### Results

### Create a policy

Policies tell Razor what operating system to install on the provisioned node, where to get the OS software, how it should be configured, and how to communicate between the node and Puppet Enterprise.

#### Procedure

1.  To create a policy that installs CentOS on machines tagged with `small`, then hands them off to Puppet Enterprise for management:

    ```
     razor create-policy --name centos-for-small
        --repo centos-6.7 --broker pe --tag small
        --hostname 'host${id}.example.com' --root-password secret
    ```


#### Results

**Note:** You can view details of a specific policy by running `razor policies <POLICY_NAME>`. You can view a table of all policies by running `razor policies`. The order in which policies are listed in the table is important because Razor applies the first matching policy to a node.

### Reinstall the node

By default, Razor protects existing nodes from reprovisioning by marking all existing nodes as installed. You must specifically instruct the server to reinstall the node in order to trigger provisioning.

#### About this task

You can skip this step if you change the `protect_new_nodes` option to `false`, which allows Razor to provision a node as soon as it PXE boots with a matching policy. Be sure you understand how the `protect_new_nodes` option works before changing it, however. Failure to protect existing nodes can result in data loss.

#### Procedure

1.  Reinstall the node: `razor reinstall-node <NODE_NAME>`


#### Results

When you reinstall the node, Razor clears the `installed` flag and the node restarts and boots into the microkernel. The microkernel reports its facts, and Razor provisions the node by applying the first applicable policy in the policy table.

When provisioning is complete, you can log into the node using the `root_password` as specified by the node’s metadata, or by the policy that the node is bound to. You can also see the node and its details in the console, and manage it there as you would any other node.

## Provision for advanced users

This workflow enables you to seamlessly provision nodes in an existing environment.

### Before you begin

You're ready to provision a node after you configure:

-   A DHCP/DNS/TFTP service with SELinux configured to enable PXE boot

-   Puppet Enterprise

-   The Razor server and client


To follow along with the examples in these workflows, you must have a new node with at least 1GB \(2GB recommended\) of memory. Don’t boot the node before you begin the provisioning process.

### About this task

In this workflow, you configure Razor objects, register any existing nodes to prevent accidentally overwriting them, and finally, load iPXE so that nodes boot through Razor. Provisioning is triggered when the node PXE boots with a matching policy in place.

The examples in this workflow demonstrate provisioning a sample node with CentOS 6.7. You can modify the settings and scale up your workflow as needed for your environment.

### Create a repository

Repositories contain – or point to – the operating system to install on a node.

#### About this task

You can create three types of repositories using specific attributes:

-   `url` – Points to content available on another server, for example, on a mirror that you maintain.

-   `iso-url` – Downloads and unpacks an ISO on the Razor server.

-   `no_content` – Creates a stub directory on the Razor server that you can manually fill with content.


#### Procedure

1.  To download a CentOS 6.7 ISO and create a repository from it:

    ```
    razor create-repo --name centos-6.7 --task centos
        --iso-url http://centos.sonn.com/6.7/isos/x86_64/CentOS-6.7-x86_64-bin-DVD1.iso
    
    ```


#### Results

The ISO is downloaded onto the Razor server, then extracted to the repository. This can take some time to complete. To monitor progress, you can run `razor commands` to view the task status or `ls -al /tmp` to see the downloaded file size.

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

### Add the `pe_repo` class to the **PE Master** node group

To manage a node handed off by the broker, the master must include a class that matches the node’s architecture.

#### About this task

**Note:** Skip this step if the node you’re provisioning has the same architecture as your master, or if the master already includes a **pe\_repo** class that matches the node’s architecture.

#### Procedure

1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

2.  On the **Configuration** tab in the **Add new class** field, select `pe_repo::platform::<VERSION>`.

    For example, `pe_repo::platform::el_7_x86_64`.

3.  Click **Add class** and then commit changes.

4.  On the master, run Puppet.


#### Results

**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

### Create a policy

Policies tell Razor what operating system to install on the provisioned node, where to get the OS software, how it should be configured, and how to communicate between the node and Puppet Enterprise.

#### Procedure

1.  To create a policy that installs CentOS on machines tagged with `small`, then hands them off to Puppet Enterprise for management:

    ```
     razor create-policy --name centos-for-small
        --repo centos-6.7 --broker pe --tag small
        --hostname 'host${id}.example.com' --root-password secret
    ```


#### Results

**Note:** You can view details of a specific policy by running `razor policies <POLICY_NAME>`. You can view a table of all policies by running `razor policies`. The order in which policies are listed in the table is important because Razor applies the first matching policy to a node.

### Register existing nodes manually

If you’re provisioning in an environment with existing nodes already installed, register the nodes to prevent Razor from re-provisioning them.

#### About this task

You must provide enough `hw-info` details so that nodes can be identified when Razor detects them.

#### Procedure

1.  To register an existing node, and indicate with the `installed` attribute that the node isn’t eligible for provisioning:

    ```
    razor register-node --hw-info net0=78:31:c1:be:c8:00 \
        --hw-info net1=72:00:01:f2:13:f0 \
        --hw-info net2=72:00:01:f2:13:f1 \
        --hw-info serial=xxxxxxxxxxx \
        --hw-info asset=Asset-1234567890 \
        --hw-info uuid="Not Settable" \
        --installed
    ```


#### Results

**Related information**  


[Protecting existing nodes](protecting_existing_nodes.md#)

[Node commands](using_the_razor_client.md#)

[Nodes API](api_reference.md#)

### Change the `protect_new_nodes` default

Because you’ve already registered existing nodes to protect them from reprovisioning, it’s now safe to change the `protect_new_nodes` default to `false`. This removes the `installed` flag from unregistered nodes so that Razor can provision them.

#### About this task

#### Procedure

1.  In the console, select **Nodes** \> **Classification**, then click the Razor server node group.

2.  On the **Configuration** tab, select the **protect\_new\_nodes** parameter, then in the **Value** field, enter false.

3.  Commit changes, run Puppet, and then restart the `pe-razor-server` service.


#### Results

**Related information**  


[Running Puppet on nodes](run_puppet_on_nodes.md#)

### Load iPXE software

Set your machines to PXE boot so that Razor can interact with the node and provision the operating system. This process uses both the `undionly.kpxe` file from the iPXE open source software stack and a Razor-specific `bootstrap.ipxe` script.

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

3.  Boot the node. This can mean physically pressing the power button, using IPMI to manage the node’s power state, or, in the case of a VM, starting the VM.


#### Results

When the node PXE boots with a policy in place, Razor detects the node and provisions it by applying the first applicable policy in the policy table.

When provisioning is complete, you can log into the node using the `root_password` as specified by the node’s metadata, or by the policy that the node is bound to. You can also see the node and its details in the console, and manage it there as you would any other node.

## Viewing information about nodes

Use these commands to view details about nodes in your environment.

|Command|Result|
|-------|------|
|`razor nodes`|Displays a list of nodes that Razor knows about.|
|`razor nodes` `<NODE_NAME>`|Displays details about the specified node.|
|`razor nodes` `<NODE_NAME>` `log`|Displays a log that includes the timing and status of installation events, as well as downloads of kickstart files and post-install scripts.|

