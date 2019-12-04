---
layout: default
title: "Provisioning a Windows node"
canonical: "/pe/latest/razor_windows_install.html"
---


This Widows-specific workflow adapts the [provisioning for new users](./razor_using.html#provisioning-for-new-users) workflow. This process enables you to learn about Razor and verify tags before provisioning nodes.

In this workflow, you load iPXE software and register nodes with the microkernel so you can view node details. Then you configure Razor objects, finishing with creating a policy.
Provisioning is triggered when you reinstall the node to remove the `installed` flag.

## Before you begin

You're ready to provision a Windows node after you've [set up a Razor environment](./razor_prereqs.html) and [installed Razor](./razor_install.html). At this point, you've configured the following:

* A DHCP/DNS/TFTP service with SELinux configured to enable PXE boot
* Puppet Enterprise
* The Razor server and client

To provision Windows machines, you also need:

* A Windows machine running the same OS that you plan to provision. This machine is used to create a WinPE image.
* (Optional) An activation key for the OS. A trial license is used if you don't have an activation key.

To follow along with the examples in this workflow, you must also have a new node with at least 8GB of memory. Don't boot the node before you begin the provisioning process.


## Configure SMB share

Because neither the WinPE environment nor the Windows installer can use an HTTP source for installation, you must use a server message block (SMB) server to store the Razor repositories.

You can configure SMB share automatically, using a Razor class parameter, or manually.

### Automatically configure SMB share

Enable the SMB share class parameter on the Razor server to let Razor set up the SMB share automatically.

1. In the PE console, select **Nodes** > **Classification**, then find and select the Razor server node group.
2. On the **Classes** tab, select the `enable_smb_share` parameter, then in the **Value** field, enter `true`.

   **Note**: If you change `enable_smb_share` from `true` to `false` later, the share remains enabled but isn't managed by Puppet.

3. Commit changes, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes), then restart the pe-razor-server service.

### Manually configure SMB share

If Samba is already installed on your Razor server, you can manually configure it to work with Razor.

1. Navigate to the Samba directory: `cd /etc/samba`.
2. Edit the `smb.conf` file:
   * Modify the network settings as necessary for your environment.
   * Edit the `global` service definition to [allow unauthenticated access](https://www.samba.org/samba/docs/man/manpages/smb.conf.5.html#idp60894080):

     ~~~
     [global]
         security     = user
         map to guest = bad user
     ~~~

   * Add a service definition for Razor that allows anonymous access and points to the `repo_store_root` class parameter of the `pe_razor` module, for example:

     ~~~
     [razor]
         comment   = Windows Installers
         path      = /opt/puppetlabs/server/data/razor-server/repo
         guest ok  = yes
         writable  = no
         browsable = yes
     ~~~

3. Restart Samba: `service smb restart`.


## Load iPXE software

Set your machines to PXE boot so that Razor can interact with the node and provision the operating system. This process uses both the `undionly.kpxe` file from the iPXE open source software stack and a Razor-specific `bootstrap.ipxe` script.

1. Download the iPXE boot image [`undionly-20140116.kpxe`](https://s3.amazonaws.com/pe-razor-resources/undionly-20140116.kpxe) and copy the image to your TFTP server's `/var/lib/tftpboot` directory:

        cp undionly-20140116.kpxe /var/lib/tftpboot

2. Download the iPXE bootstrap script from the Razor server and copy the script to your TFTP server's `/var/lib/tftpboot` directory:

        wget "https://$<RAZOR_HOSTNAME>:$<HTTPS_PORT>/api/microkernel/bootstrap?nic_max=1&http_port=$<HTTP_PORT>" -O /var/lib/tftpboot/bootstrap.ipxe

   **Note**: Don't use `localhost` as the name of the Razor host. The bootstrap script chain-loads the next iPXE script from the Razor server, so it must contain the correct host name.

## Register a node with the microkernel

Registering a node lets you learn about the node before Razor provisions it. With registered nodes, you can view facts about the node, add metadata to the node, and see which tags the node matches.

1. Boot the node. This can mean physically pressing the power button, using IPMI to manage the node's power state, or, in the case of a VM, starting the VM.

   The node boots into the microkernel and Razor discovers the node. After the initial PXE boot, the node downloads the microkernel.

2. On the Razor server, view the new node: `razor nodes`.

   The output displays the new node ID and name, like this:

        id: "http://localhost:8150/api/collections/node/node1"
        name: "node1"
        spec: "/razor/v1/collections/nodes/member"

3. View the node's details: `razor nodes <NODE_NAME>`.

   The output displays hardware and DHCP information, the path to the log, and a placeholder for tags, like this:

        hw_info:
                mac: ["08-00-27-8a-5e-5d"]
             serial: "0"
               uuid: "9a717dc3-2392-4853-89b9-27fec1aec7b2"
        dhcp_mac: "08-00-27-8a-5e-5d"
        log:
                log => http://localhost:8150/api/collections/node/node1/log
        tags: []

4. When the microkernel is running on the machine, view facts about the node: `razor nodes <NODE_NAME> facts`

   Facter periodically sends facts back to the server about the node, including IP address, details about network cards, and block devices.


## Build a WinPE image

Create a custom Windows Preinstallation Environment (WinPE) WIM image containing Razor scripts.

1. On an existing Windows machine running the same OS that you plan to install, install the [Windows assessment and deployment kit](http://msdn.microsoft.com/en-us/library/windows/hardware/hh825486.aspx) in the default location.
2. Copy the `build-winpe` directory from the Razor server to the existing Windows machine. If the directory is absent from your Razor server, use [this archive](https://s3.amazonaws.com/pe-razor-resources/build-winpe.zip).
3. Change into the directory you created in the previous step, for example, `c:\build-winpe`.
4. (Optional) To include additional Windows drivers in the WIM image -- for example, if your machines require proprietary UCS drivers -- place the .inf files in the `extra-drivers` folder.
5. Run one of these build scripts, depending on whether you're using unsigned drivers:

   * Standard build script -- `powershell -executionpolicy bypass -noninteractive -file build-razor-winpe.ps1 -razorurl http://razor:8150/svc`
   * Build script for unsigned drivers -- `powershell -executionpolicy bypass -noninteractive -file build-razor-winpe.ps1 -razorurl http://razor:8150/svc -allowunsigned`

   When the build script completes, a `razor-winpe.wim` image appears in a new `razor-winpe` directory inside the current working directory. <!--In PE versions prior to 2015.3, the image is named winpe.wim.-->


## Create a repository and add the WinPE image

Because Razor can't unpack Windows DVD images, you must create a stub repository and manually fill it with content.

1. Copy the Windows ISO image to the Razor server.
2. Create an empty repository, for example:

       razor create-repo --name win2012r2 --task windows/2012r2
           --no-content true

3. After the repository is created, log into your Razor server as
root, change into the repository directory, `/opt/puppetlabs/server/data/razor-server/<REPO_NAME>`, then mount the ISO image: <!--In PE 3.8.x, the repo_store_root directory is located at /opt/puppet/var/razor/<REPO_NAME>.-->

        $ mount -o loop </PATH/TO/WINDOWS_SERVER.ISO> /mnt
        $ cp -pr /mnt/* <REPO_NAME>
        $ umount /mnt
        $ chown -R pe-razor: <REPO_NAME>

   **Note**: The repository directory is specified by the `repo_store_root` class parameter of the `pe_razor` module. Your directory might be different from the default if you customized this parameter.

4. Copy the WinPE image from the existing Windows machine to the repository directory on the Razor server.


Related links:

* [Repositories](./razor_repos.html)
* [create-repo client command](./razor_client_commands.html#create-repo)
* [create-repo API](./razor_reference.html#create-new-repository-create-repo)


## (Optional) Create tags

Tags let you group nodes based on their characteristics. You can then apply policies based on tags to install appropriate operating systems on tagged nodes. If you don't specify tags for a policy, the policy binds to any node.

1. To create a tag called *large* with a rule that matches any machine with more than 8GB of memory:

        razor create-tag --name large
          --rule '[">", ["num", ["fact", "memorysize_mb"]], 8256]'

2. To inspect the tag on the server, run `razor tags <TAG_NAME>`.

   For example, `razor tags large` responds with:

        From https://razor:8151/api/collections/tags/large:
            name: large
            rule: [">", ["num", ["fact", "memorysize_mb"]], 8256]
           nodes: 1
        policies: 0

3. Confirm that expected nodes now have the tag: `razor tags <TAG_NAME> nodes`.

   For example, `razor tags large nodes` displays a table of nodes with more than 8GB of memory.

**Tip**: To see details about the policies associated with a tag, run `razor tags <TAG_NAME> policies`. To see its rule, run `razor tags <TAG_NAME> rule`.

Related links:

* [Tags](./razor_tags.html)
* [create-tag client command](./razor_client_commands.html#create-tag)
* [create-tag API](./razor_reference.html#create-tag-create-tag)


## (Optional) Create a broker

Brokers hand off nodes to configuration management systems like PE.

1. To hand off Razor nodes to a Puppet master at `puppet-master.example.com`:

       razor create-broker --name pe --broker-type puppet-pe
         --configuration server=puppet-master.example.com

Related links:

* [Brokers](./razor_brokers.html)
* [create-broker client command](./razor_client_commands.html#create-broker)
* [create-broker API](./razor_reference.html#create-broker-create-broker)


#### Add `pe_repo` to PE master

In order to manage the nodes handed off by the broker, the Puppet master must include a class that matches the node's architecture.

You can skip this step if the Puppet master already includes a `pe_repo` class that matches the node's architecture.

1. In the PE console, click **Nodes** > **Classification**, then in the **PE Infrastructure** node group, select the **PE Master** node group.
2. In the **Add new class** field, select `pe_repo::platform::<VERSION>`.

   For example, `pe_repo::platform::windows_x86_64`.

3. Click **Add class**, then commit changes.

4. On the Puppet master, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes).


## Create a policy

Policies tell Razor what OS to install on the provisioned node, where to get the OS software, how it should be configured, and how to communicate between the node and PE.

1. To create a policy that installs Windows on machines tagged with `large`, then hands them off to PE for management:

        razor create-policy --name windows-for-large
           --repo win2012r2 --broker pe --tag large
           --hostname 'host${id}.example.com' --root-password secret

**Tip**: You can view details of a specific policy by running `razor policies <POLICY_NAME>`. You can view a table of all policies by running `razor policies`. The order in which policies are listed in the table is important because Razor applies the first matching policy to a node.

Related links:

* [Policies](./razor_policies.html)
* [create-policy client command](./razor_client_commands.html#create-policy)
* [create-policy API](./razor_reference.html#create-policy-create-policy)


## Reinstall node

By default, Razor protects existing nodes from reprovisioning by marking all existing nodes as `installed`. You must specifically instruct the server to reinstall the node in order to trigger provisioning.

You can skip this step if you change the `protect_new_nodes` option to `false`, which allows Razor to provision a node as soon as it PXE boots with a matching policy. Be sure you understand how the `protect_new_nodes` option works before changing it, however. Failure to protect existing nodes can result in data loss.

1. Reinstall the node: `razor reinstall-node <NODE_NAME>`.


> **Result**: When you reinstall the node, Razor clears the `installed` flag and the node restarts and boots into the microkernel. The microkernel reports its facts, and Razor provisions the node by applying the first applicable policy in the policy table.
>
> When provisioning is complete, you can log into the node using the `root_password` as specified by the node's metadata, or by the policy that the node is bound to. You can also see the node and its details in the PE console, and manage it there as you would any other node.

* * *
