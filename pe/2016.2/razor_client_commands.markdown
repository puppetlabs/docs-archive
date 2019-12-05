---
layout: default
title: "Razor client commands"
canonical: "/pe/latest/razor_client_commands.html"

---

Use the Razor client to interact with Razor and provision bare metal nodes.


## Configuration commands

Razor configuration is pulled from a configuration file controlled by PE. You can change configuration values in the console with class parameters of the `pe_razor` module.

**Note**: In order for configuration changes to take effect, you must restart the Razor service by running `service pe-razor-server restart` on the Razor server.

### `config`

The `config` command displays details about your Razor configuration.

**Note**: Properties specified in the `api_config_blacklist` aren't returned by the `config` command.

#### Sample command

To view details about your Razor configuration:

    razor config



## Node commands

Nodes are created either through the node boot endpoint, when the node initiates its first web request to the Razor server, or through the `register-node` command. Both methods create a stub for the node in the Razor database. When the node boots into the microkernel, it gathers facts and reports them to the node checkin endpoint, linking new facts to the stub. At that point, the node is fully registered in the Razor system and goes through the binding process.

### `register-node`

The `register-node` command enables you to identify a node before it's discovered. This can be useful to apply metadata before the node boots up, or to indicate to Razor that it shouldn't provision -- or reprovision -- a node that's already installed.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>hw_info</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><p>Hardware information about the node.</p>
     <p>You must include enough information that the node can be identified by hardware information sent by the firmware when the node boots. This usually includes the MAC addresses of all network interfaces.</p><p>This attribute can include some or all of these entries:
     <ul>
      <li><code>netN</code> — The MAC address of each network interface. The order of addresses isn't significant.</li>
      <li><code>serial</code> — DMI serial number of the node.</li>
      <li><code>asset</code> — DMI asset number of the node.</li>
      <li><code>uuid</code> — DMI universally unique identifier of the node.</li>
     </ul></p>
    </td>
  </tr>
  <tr>
    <td>installed</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><code>true</code> to indicate that the node shouldn't be provisioned — or reprovisioned — by Razor.</td>
  </tr>
</table>

#### Sample command

To register an installed machine before it's booted, and prevent reprovisioning:

      razor register-node --hw-info net0=78:31:c1:be:c8:00 \
          --hw-info net1=72:00:01:f2:13:f0 \
          --hw-info net2=72:00:01:f2:13:f1 \
          --hw-info serial=xxxxxxxxxxx \
          --hw-info asset=Asset-1234567890 \
          --hw-info uuid="Not Settable" \
          --installed



### `set-node-hw-info`

The `set-node-hw-info` command sets or updates the hardware info for a specified node. This is useful when a node's hardware changes, such as when you replace a network card.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>node</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the node to update.</td>
  </tr>
  <tr>
    <td>hw_info</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><p>Hardware information about the node.</p> <p>This attribute can include some or all of these entries:
     <ul>
      <li><code>netN</code> — The MAC address of each network interface. The order of addresses isn't significant.</li>
      <li><code>serial</code> — DMI serial number of the node.</li>
      <li><code>asset</code> — DMI asset number of the node.</li>
      <li><code>uuid</code> — DMI universally unique identifier of the node.</li>
     </ul></p>
    </td>
  </tr>
</table>

#### Sample command

To update node172 with new hardware information:

    razor set-node-hw-info --node node172 \
      --hw-info net0=78:31:c1:be:c8:00 \
      --hw-info net1=72:00:01:f2:13:f0 \
      --hw-info net2=72:00:01:f2:13:f1 \
      --hw-info serial=xxxxxxxxxxx \
      --hw-info asset=Asset-1234567890 \
      --hw-info uuid="Not Settable"



### `delete-node`

The `delete-node` command deletes a specified node from the Razor database. The `delete-node` command is similar to `reinstall-node`, except that `delete-node` doesn't retain log information or the node number.

>**Note**: If the deleted node boots again at some point, Razor automatically recreates the node as if it were the first time it contacted the Razor server.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:------------:|:----------------------:|-------------------------
name            | ✓           | 1                       | Name of the node to delete.

#### Sample command

To delete node17:

    razor delete-node --name node17



### `reinstall-node`

The `reinstall-node` command clears a node's `installed` flag and -- by default -- removes its association with policies. You can use the `same-policy` attribute to retain the assigned policy.

After restart, the node boots into the microkernel, goes through
discovery and tag matching, and can bind to another policy for reinstallation. This command doesn't change the node's metadata or facts.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the node to reinstall.</td>
  </tr>
  <tr>
    <td>same-policy</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p><code>true</code> to retain the same policy for reinstallation.</p>
     <p>If omitted, the node can bind to a different policy when reinstalled.</p></td>
  </tr>
</table>

#### Sample commands

To reinstall node17:

    razor reinstall-node --name node17

To reinstall node17 and retain its assigned policy:

    razor reinstall-node --name node17 --same-policy



## IPMI commands

IPMI commands are node commands based on the Intelligent Platform Management Interface.

> **Note**: You must install the `ipmitool` on the Razor server before using IPMI commands. To install the tool, run `yum install ipmitool -y`.

### `set-node-ipmi-credentials`

The `set-node-ipmi-credentials` sets or updates the host name, user name, or password for connecting with a BMC/LOM/IPMI LAN or LANplus service.

The command works only with remote IPMI targets, not local targets.

After IPMI credentials have been set up for a node, you can use the `reboot-node` and `set-node-desired-power-state` commands.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the node to update.</td>
  </tr>
  <tr>
    <td>ipmi_hostname</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td>IPMI host name or IP address of the BMC of the host.</td>
  </tr>
  <tr>
    <td>ipmi_username</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td>IPMI LANplus username, if any, for this BMC.</td>
  </tr>
  <tr>
    <td>ipmi_password</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td>IPMI LANplus password, if any, for this BMC.</td>
  </tr>
</table>

#### Sample command

To set IPMI credentials for `node17`:

    razor set-node-ipmi-credentials --name node17 \
      --ipmi-hostname bmc17.example.com \
      --ipmi-username null \
      --ipmi-password sekretskwirrl



### `reboot-node`

The `reboot-node` command triggers a hard power cycle on a specified node using IPMI credentials.

> **Note**: You must specify IPMI credentials before using the `reboot-node` command.

If an execution slot isn't available on the target node, the `reboot-node` command is queued and runs as soon as a slot is available. There are no limits on how many commands you can queue up, how frequently a node can be rebooted, or how long a command can stay in the queue. If you restart your Razor server before queued commands are executed, they remain in the queue and run after the server restarts.

The `reboot-node` command is not integrated with IPMI power state monitoring, so you can't see power transitions in the record or when polling the node object.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:------------:|:----------------------:|-------------------------
name            | ✓           | 1                       | Name of the node to reboot.

#### Sample command

To queue a node reboot:

    razor reboot-node --name node1



### `set-node-desired-power-state`

The `set-node-desired-power-state` command specifies whether you want a node to remain powered off or on. By default, Razor checks node power state every few minutes in the
background. If it detects a node in a non-desired state, Razor issues an IPMI command directing the node to its desired state.

> **Note**: You must specify IPMI credentials before using the `set-node-desired-power-state` command.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:------------:|:----------------------:|-------------------------
name            | ✓           | 1                       | Name of the node to power off or on.
to              |             | 2                      | Desired power state: `on`, `off`, or `null`, meaning that Razor shouldn't enforce a power state.

#### Sample commands

To tell Razor you want node1234 powered on and kept on:

    razor set-node-desired-power-state --name node1234 \
      --to on




## Node metadata commands



### `update-node-metadata`

The `update-node-metadata` command sets or updates a single metadata key.

#### Command attributes

Attributes      | Required     | Positional arguments | Description
----------------|:------------:|:--------------------:|-------------------------
node            | ✓            | 1                    | Name of the node for which to modify metadata.
key             | ✓            | 2                    | Name of the key to modify.
value           | ✓            | 3                    | New value for the key.
no_replace       |              |                      | `true` to cancel the update operation if the specified key is already present.

#### Sample commands

To update the `my_key` key for a node:

    razor update-node-metadata --node node1 \
      --key my_key --value twelve



### `modify-node-metadata`

The `modify-node-metadata` command sets, updates, or clears metadata key-value pairs.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>node</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the node for which to modify metadata.</td>
  </tr>
  <tr>
    <td>update</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td>Key and value pair to update.</td>
  </tr>
  <tr>
    <td>remove</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td>Key and value pair to remove.</td>
  </tr>
  <tr>
    <td>clear</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td><code>true</code> to clear all metadata.</td>
  </tr>
  <tr>
    <td>no_replace</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><code>true</code> to cancel the update operation if the specified key is already present.</td>
  </tr>
</table>
\* You must specify one of three attributes: `update`, `remove`, or `clear`.

#### Sample commands

To add values for key1 and key2 to a node, but not if they are already set, and to remove key3 and key4:

      razor modify-node-metadata --node node1 --update key1=value1 \
          --update key2='[ "val1", "val2", "val3" ]' --remove key3 --remove key4
          --noreplace

To remove all node metadata:

      razor modify-node-metadata --node node1 --clear



### `remove-node-metadata`

The `remove-node-metadata` command removes either a single metadata entry or all metadata entries on a node.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>node</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the node for which to modify metadata.</td>
  </tr>
  <tr>
    <td>key</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;">2</td>
    <td>Name of the key to remove.</td>
  </tr>
  <tr>
    <td>all</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td><code>true</code> to remove all metadata about the node.</td>
  </tr>
</table>
\* You must specify one of two attributes: `key` or `all`.

#### Sample commands

To remove a single key from a node:

      razor remove-node-metadata --node node1 --key my_key

To remove all keys from a node:

      razor remove-node-metadata --node node1 --all




## Repository commands



### `create-repo`

The `create-repo` command creates a new repository. The repository can contain the content to install a node, or it can point to an existing online repository.

You can create three types of repositories:

* Those that reference content available on another server, for example, on a mirror you maintain (`url`).
* Those where Razor unpacks ISOs for you and serves their contents (`iso_url`).
* Those where Razor creates a stub directory that you can manually fill with content (`no_content`).

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name for the new repository.</td>
  </tr>
  <tr>
    <td>url</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td>URL of a remote repository.</td>
  </tr>
  <tr>
    <td>iso_url</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td><p>URL of an ISO image to download and unpack in a new, local repository.</p>
     <p>You can use an HTTP or HTTPS URL, or a file URL. If you're using a file URL, manually place the ISO image on the Razor server before you call the command.</p>
     <p>If you supply the <code>iso_url</code> attribute to create a repository, you can delete it from the server later using the <code>delete-repo</code> command.</p></td>
  </tr>
    <td>no_content</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td><p>Creates a stub directory in the repo store where you can manually extract an image.</p>
     <p>After this command finishes, you can log into your Razor server and fill the repository directory with the content, for example by loopback-mounting the install media and copying it into the directory. The repository directory is specified by the <code>repo_store_root</code> class parameter of the <code>pe_razor</code> module. By default, the directory is <code>/opt/puppetlabs/server/data/razor-server/repo</code>.</p>
     <p>This attribute is usually necessary for Windows install media, because the library that Razor uses to unpack ISO images doesn't support Windows ISO images.</p></td>
  </tr>
  <tr>
    <td>task</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><p>Name of the default task that installs nodes from this repository.</p>
     <p>We recommend that the task match the operating system specified by the <code>url</code> or <code>iso_url</code>.</p>
     <p>You can override this paramater at the policy level.</p>
     <p><b>Tip</b>: You can use <code>razor tasks</code> to see which tasks are available on your server.</p></td>
  </tr>
</table>
\* You must specify one of the attributes `url`, `iso_url`, or `no_content`.

#### Sample commands

To create a repository from an ISO image, which is downloaded and unpacked
  by the Razor server in the background:

      razor create-repo --name fedora21 \
          --iso-url http://example.com/Fedora-21-x86_64-DVD.iso \
          --task fedora

To unpack an ISO image from a file on a server without uploading the file from the client:

      razor create-repo --name fedora21 \
          --iso-url file:///tmp/Fedora-21-x86_64-DVD.iso \
          --task fedora

To point to a resource without downloading content to the Razor server:

      razor create-repo --name fedora21 --url \
          http://mirrors.n-ix.net/fedora/linux/releases/21/Server/x86_64/os/ \
          --task fedora

To create a stub directory that you can manually fill with content:

      razor create-repo --name fedora21 --no-content --task Fedora


### `update-repo-task`

The `update-repo-task` command specifies the task that installs the contents of the repository.

If a node is currently provisioning against the repository when you run this command, provisioning might fail.

#### Command attributes

Attributes  | Required     | Positional arguments   | Description
------------|:------------:|:----------------------:|-------------------------
repo        | ✓           | 1                      | Name of an existing repository to update.
task        | ✓           | 2                      | Name of the task to be used by the repository.

#### Sample commands

To update the `my_repo` repository to the task `other_task`:

    razor update-repo-task --repo my_repo --task other_task



### `delete-repo`

The `delete-repo` command deletes a specified repository from the Razor database.

Before deleting a repository, remove any references to it in existing policies. This command fails if the repository is in use by an existing policy.

If you supplied the `iso_url` property when you created the repository, the folder is also deleted from the server. If you didn't supply the `iso_url` property, content remains in the repository directory.

#### Command attributes

Attributes  | Required     | Positional arguments    | Description
------------|:---------------:|:-------------------:|-------------------------
name        | ✓             | 1                     | Name of the repository to delete.

#### Sample commands

To delete an obsolete repository:

    razor delete-repo --name my_obsolete_repo





## Task commands



### `create-task`

The `create-task` command creates a task in the Razor database. This command is an alternative to manually placing task files in the `task_path`. If you anticipate needing to make changes to tasks, we recommend the disk-backed task approach.

Razor has a set of tasks for installing on supported operating systems. See [the razor-server Github page](https://github.com/puppetlabs/razor-server/tree/master/tasks) for more information.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name for the new task.</td>
  </tr>
  <tr>
    <td>os</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td>Name of the OS installed by the task.</td>
  </tr>
  <tr>
    <td>templates</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td>Named templates used for task stages.</td>
  </tr>
  <tr>
    <td>boot_seq</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>List of key-value pairs that describe templates in the order they're installed.</p>
     <p>Use the optional <code>default</code> hash key to specify the default template to use when no other template applies.</p></td>
  </tr>
</table>

#### Sample command

To define the RedHat task included with Razor:

    razor create-task --name redhat-new --os "Red Hat Enterprise Linux" \
      --description "A basic installer for RHEL6" \
      --boot-seq 1=boot_install --boot_seq default=boot_local \
      --templates "boot_install=#!ipxe\necho Razor <%= task.label %> task boot_call\necho Installation node: <%= node_url  %>\necho Installation repo: <%= repo_url %>\n\nsleep 3\nkernel <%= repo_url("/isolinux/vmlinuz") %> <%= render_template("kernel_args").strip %> || goto error\ninitrd <%= repo_url("/isolinux/initrd.img") %> || goto error\nboot\n:error\nprompt --key s --timeout 60 ERROR, hit 's' for the iPXE shell; reboot in 60 seconds && shell || reboot\n" \
      --templates kernel_args="ks=<%= file_url("kickstart") %> network ksdevice=bootif BOOTIF=01-${netX/mac}" \
      --templates kickstart="#!/bin/bash\n# Kickstart for RHEL/CentOS 6\n# see: http://docs.redhat.com/docs/en-US/Red_Hat_Enterprise_Linux/6/html/Installation_Guide/s1-kickstart2-options.html\n\ninstall\nurl --url=<%= repo_url %>\ncmdline\nlang en_US.UTF-8\nkeyboard us\nrootpw <%= node.root_password %>\nnetwork --hostname <%= node.hostname %>\nfirewall --enabled --ssh\nauthconfig --enableshadow --passalgo=sha512 --enablefingerprint\ntimezone --utc America/Los_Angeles\n# Avoid having 'rhgb quiet' on the boot line\nbootloader --location=mbr --append="crashkernel=auto"\n# The following is the partition information you requested\n# Note that any partitions you deleted are not expressed\n# here so unless you clear all partitions first, this is\n# not guaranteed to work\nzerombr\nclearpart --all --initlabel\nautopart\n# reboot automatically\nreboot\n\n# following is MINIMAL https://partner-bugzilla.redhat.com/show_bug.cgi?id=593309\n%packages --nobase\n@core\n\n%end\n\n%post --log=/var/log/razor.log\necho Kickstart post\ncurl -s -o /root/razor_postinstall.sh <%= file_url("post_install") %>\n\n# Run razor_postinstall.sh on next boot via rc.local\nif [ ! -f /etc/rc.d/rc.local ]; then\n  # On systems using systemd /etc/rc.d/rc.local does not exist at all\n  # though systemd is set up to run the file if it exists\n  touch /etc/rc.d/rc.local\n  chmod a+x /etc/rc.d/rc.local\nfi\necho bash /root/razor_postinstall.sh >> /etc/rc.d/rc.local\nchmod +x /root/razor_postinstall.sh\n\ncurl -s <%= stage_done_url("kickstart") %>\n%end\n############\n" \
      --templates post_install="#!/bin/bash\n\nexec >> /var/log/razor.log 2>&1\n\necho "Starting post_install"\n\n# Wait for network to come up when using NetworkManager.\nif service NetworkManager status >/dev/null 2>&1 && type -P nm-online; then\n    nm-online -q --timeout=10 || nm-online -q -x --timeout=30\n    [ "$?" -eq 0 ] || exit 1\nfi\n\n<%= render_template("set_hostname") %>\n\n<%= render_template("store_ip") %>\n\n<%= render_template("os_complete") %>\n\n# We are done\ncurl -s <%= stage_done_url("finished") %>\n"





## Tag commands



### `create-tag`

The `create-tag` command creates a new tag and sets the rules used to apply the tag to nodes.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name for the new tag.</td>
  </tr>
  <tr>
    <td>rule</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">2</td>
    <td><p>Case-sensitive match expression that applies the tag to a node if the expression evaluates as true.</p>
     <p>For more information on tag rules, see <a href="./razor_tags.html">Tags</a>.</p></td>
  </tr>
</table>

#### Sample commands

 To create a tag that's applied to nodes with two processors:

      razor create-tag --name small --rule '["=", ["fact", "processorcount"], "2"]'



### `update-tag-rule`

The `update-tag-rule` changes the rule of a specified tag.

After updating a tag, Razor reevaluates the tag against all nodes. With the `--force` flag, the tag is reevaluated even if it's used by policies.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of an existing tag to update.</td>
  </tr>
  <tr>
    <td>rule</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">2</td>
    <td><p>Case-sensitive match expression that applies the tag to a node if the expression evaluates as true.</p>
     <p>For more information on tag rules, see <a href="./razor_tags.html">Tags</a>.</p></td>
  </tr>
  <tr>
    <td>force</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td>Reevaluates tags used by an existing policy.</td>
  </tr>
</table>

#### Sample command

To update a tag rule and reevaluate nodes with the tag, even if the tag is used by a policy:

    razor update-tag-rule --name small --force \
          --rule '["<=", ["fact", "processorcount"], "2"]'




### `delete-tag`

The `delete-tag` command deletes a specified tag.

With the `--force` flag, the tag is deleted and removed from any policies that use it.

#### Command attributes

Attributes  | Required     | Positional arguments  | Description
------------|:---------------:|:-------------------:|-------------------------
name        | ✓             | 1                     | Name of an existing tag to delete.
force       |               |                       | Deletes tags used by an existing policy.

#### Sample commands

To delete an unused tag:

    razor delete-tag --name my_obsolete_tag

To delete a tag whether or not it's used:

    razor delete-tag --name my_obsolete_tag --force





## Policy commands

Policies govern how nodes are provisioned. Razor maintains an ordered table of policies. When a node boots, Razor traverses the table to find the first eligible policy for that node. A policy might be ineligible for binding to a node if the node doesn't contain all of the tags on the policy, if the policy is disabled, or if the policy has reached its maximum for the number of allowed nodes.

When you list the `policies` collection, the list is in the order that
Razor checks policies against nodes.



### `create-policy`

The `create-policy` command creates a new policy that determines how nodes are provisioned. Razor maintains an ordered table of policies, and applies the first policy that matches a node.

>**Tip**: You can create policies in a JSON file to make saving and modifying them easier. To apply a policy with a JSON file, use `razor create-policy --json <FILENAME>.json`.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name for the new policy.</td>
  </tr>
  <tr>
    <td>hostname</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><p>Pattern for host names of nodes bound to this policy.</p>
     <p>The <code>${id}</code> references each node's internal ID number.</p></td>
  </tr>
  <tr>
    <td>root_password</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><p>Root password for the new system.</p>
     <p>Valid values depend on how the task renders passwords.</p></td>
  </tr>
  <tr>
    <td>enabled</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p><code>true</code> if the policy is enabled upon creation.</p>
     <p>If omitted, the policy is enabled.</p></td>
  </tr>
  <tr>
    <td>max_count</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Number indicating how many nodes can bind to the policy.</p>
     <p>If omitted, the policy can bind to an unlimited number of nodes.</p></td>
  </tr>
  <tr>
    <td>before</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Name of the policy that this policy is placed before in the policy list.</p>
     <p>This attribute can't be used with <code>after</code>.</p></td>
  </tr>
  <tr>
    <td>after</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Name of the policy that this policy is placed after in the policy list.</p>
     <p>This attribute can't be used with <code>before</code>.</p></td>
  </tr>
  <tr>
    <td>tags</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Names of tags that a node must match to qualify for the policy.</p>
     <p>A node must have all tags specified for the policy to apply.</p></td>
  </tr>
  <tr>
    <td>repo</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td>Name of the repository that contains the operating system installed by this policy.</td>
  </tr>
  <tr>
    <td>broker</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><p>Name of the broker to use after provisioning to hand off the node to a management system.</p>
     <p>If you don't want to hand off management, use <code>noop</code>.</p></td>
  </tr>
  <tr>
    <td>task</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Name of the task to install nodes that bind to this policy.</p>
     <p>If omitted, the <code>task</code> property of the policy's <code>repo</code> is used.</p></td>
  </tr>
  <tr>
    <td>node_metadata</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Metadata to apply to the node when this policy is bound.</p>
     <p>Existing metadata is not overwritten.</p></td>
  </tr>
</table>


#### Sample command

To create a policy that installs CentOS 6.4:

      razor create-policy --name centos-for-small \
    --repo centos-6.4 --task centos --broker noop \
    --enabled --hostname "host${id}.example.com" \
    --root-password secret --max-count 20 \
    --before "other policy" --tag small --node-metadata key=value



### `move-policy`

The `move-policy` command lets you change the order in which Razor considers policies for matching against nodes.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the policy to move.</td>
  </tr>
  <tr>
    <td>before</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td>Name of the policy that this policy is placed before in the policy list.</td>
  </tr>
  <tr>
    <td>after</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td>Name of the policy that this policy is placed after in the policy list.</td>
  </tr>
</table>
\* You must specify one of the two ordering attributes, `before` or `after`.

#### Sample commands

To move a policy before another policy:

    razor move-policy --name policy --before succeedingpolicy

To move a policy after another policy:

    razor move-policy --name policy --after precedingpolicy


### `enable-policy` and `disable-policy`

The `disable-policy` command prevents the specified policy from binding to any nodes. This can be useful if you want to temporarily inactivate a policy without deleting it. For example, you can use `disable-policy` to prevent nodes from installing a certain OS for a short period.

The `enable-policy` command reactivates a disabled policy.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:---------------:|:-------------------:|-------------------------
name            | ✓             | 1                     | Name of the policy to disable or enable.

#### Sample commands

To disable a policy:

    razor disable-policy --name my_policy

To enable a policy:

    razor enable-policy --name my_policy


### `modify-policy-max-count`

The `modify-policy-max-count` command sets or removes the limit on the maximum number of nodes that can bind to a policy.

>**Note**: To reduce the number of nodes a policy can bind to, you must use the `reinstall-node` command to mark nodes as uninstalled.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the policy to modify.</td>
  </tr>
  <tr>
    <td>max_count</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;">2</td>
    <td>Maximum number of nodes the policy can bind to.</td>
  </tr>
  <tr>
    <td>no_max_count</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td>Removes any limits on the number of nodes that can bind to the policy.</td>
  </tr>
</table>
\*You must specify one of the two count attributes, `max_count` or `no_max_count`.

#### Sample commands

To allow a policy to match an unlimited number of nodes:

    razor --name example --no-max-count

To set a policy to match a maximum of 15 nodes:

    razor --name example --max-count 15


### `add-policy-tag`

The `add-policy-tag` command adds a new or existing tag to a policy. Because binding to policies happens only when unbound nodes check in, adding a policy tag doesn't affect nodes that are already bound to a policy.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the policy to modify.</td>
  </tr>
  <tr>
    <td>tag</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">2</td>
    <td>Name of the tag to add to the policy.</td>
  </tr>
  <tr>
    <td>rule</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;">3</td>
    <td><p>Creates a new tag using a case-sensitive match expression that applies the tag to a node if the expression evaluates as true.</p>
    <p>For more information on tag rules, see <a href="./razor_tags.html">Tags</a>.</p>
    <p>If tag creation fails, the policy isn't modified.</p></td>
  </tr>
</table>

#### Sample commands

To add the existing tag `virtual` to the policy `example`:

      razor add-policy-tag --name example --tag virtual

To add a new tag `virtual` to the policy `example`:

      razor add-policy-tag --name example --tag virtual \
          --rule '["=", ["fact", "virtual", "false"], "true"]'


### `remove-policy-tag`

The `remove-policy-tag` command removes a tag from a policy. Because binding to policies happens only when unbound nodes check in, adding a policy tag doesn't affect nodes that are already bound to a policy.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:------------:|:----------------------:|-------------------------
name            | ✓            | 1                      | Name of the policy to modify.
tag             | ✓            | 2                      | Name of the tag to remove from the policy.

#### Sample commands

To remove the tag `virtual` from the policy `example`:

      razor remove-policy-tag --name example --tag virtual



### `update-policy-task`

The `update-policy-task` command adds or removes a task from a policy.

>**Tip**: Before updating a policy task, use `razor policies <POLICYNAME> nodes` to verify that no nodes are currently provisioning against the policy. If a node is provisioning against the policy when you run this command, provisioning might fail.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:------------:|:----------------------:|-------------------------
policy          | ✓            | 1                      | Name of the policy to modify.
task            |              | 2                      | Name of the task to add to the policy.
no_task         |              |                       | `true` if the policy uses the task in the repository.

#### Sample commands

To update a policy's task to a task named `other_task`:

      razor update-policy-task --policy my_policy --task other_task

To use the task specified by the policy's repository:

      razor update-policy-task --policy my_policy --no-task



### `delete-policy`

The `delete-policy` command deletes a policy from the Razor database. Nodes bound to the policy aren't re-provisioned.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:------------:|:----------------------:|-------------------------
name           | ✓            | 1                      | Name of the policy to delete.

#### Sample commands

To delete an obsolete policy:

      razor delete-policy --name my_obsolete_policy




## Broker commands



### `create-broker`

The `create-broker` command creates a new broker configuration used to hand off management of nodes.

If you're using Puppet Enterprise for node management, use the `puppet-pe` broker type. For details on creating other types of brokers, see [Create a new broker type](./razor_brokers.html#create-a-new-broker-type).

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name for the new broker.</td>
  </tr>
  <tr>
    <td>broker_type</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">2</td>
    <td>Usually <code>puppet-pe</code>. Other valid values are <code>puppet</code>, to transfer control to an open source puppet master, and <code>noop</code>, to leave the node unmanaged.</td>
  </tr>
  <tr>
    <td>configuration</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td>Configuration details specific to the <code>broker_type</code>. For <code>puppet_pe</code> brokers, properties include:
     <ul>
      <li><code>server</code> -- Host name of the Puppet master.</li>
      <li><code>version</code> -- The agent version to install. The default value is <code>current</code>.</li>
      <li><code>windows_download_url</code> -- Download URL for a Windows PE agent installer. The default value is taken from the version config file.</li>
     </ul>
    </td>
  </tr>
</table>

#### Sample command

To create a simple `puppet-pe` broker:

    razor create-broker --name puppet-pe -c server=puppet.example.org \
      -c version=2015.3



### `update-broker-configuration`

The `update-broker-configuration` command sets or clears a specified key value for a broker.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>broker</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the broker to update.</td>
  </tr>
  <tr>
    <td>key</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">2</td>
    <td><p>Name of the key to modify in the broker's configuration file.</p>
     <p>For <code>puppet-pe</code> brokers, this is usually <code>server</code> or <code>version</code>.</p></td>
  </tr>
  <tr>
    <td>value</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;">3</td>
    <td><p>New value to use for the key.</p>
     <p>This attribute can't be used with <code>clear</code>.</p></td>
  </tr>
  <tr>
    <td>clear</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p><code>true</code> to clear the value of the specified key.</p>
     <p>This attribute can't be used with <code>value</code>.</p></td>
  </tr>
</table>

#### Sample command

To change the key `some_key` to `new_value`:

    razor update-broker-configuration --broker mybroker --key some_key --value new_value



### `delete-broker`

The `delete-broker` command deletes a specified broker from the Razor database.

Before deleting a broker, remove any references to it in policies. This command fails if a policy is using the broker.

#### Command attributes

Attributes  | Required     | Positional arguments    | Description
------------|:---------------:|:-------------------:|-------------------------
name        | ✓             | 1                     | Name of the broker to delete.

#### Sample command

To delete an obsolete broker:

    razor delete-broker --name my_obsolete_broker




## Hook commands

Hooks are custom, executable scripts that are triggered to run when a node hits certain phases in its lifecycle. A hook script receives several properties as input, and can make changes in the node's metadata or the hook's internal configuration.

Hooks can be useful for:

- Notifying an external system about the stage of a node's installation.
- Querying external systems for information that modifies how a node gets installed.
- Calculating complex values for use in a node's installation configuration.

### `create-hook`

The `create-hook` command enables a hook to run when specified events occur.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the new hook.</td>
  </tr>
  <tr>
    <td>hook_type</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">2</td>
    <td>Type of hook that the new hook is based on. For available hook types on your server, run <code>razor create-hook --help</code>.</td>
  </tr>
  <tr>
    <td>configuration</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Configuration settings as required by the <code>hook_type</code>.</p>
    <p>This argument sets the initial configuration values for a hook. Configuration values can change as the hook is executed based on events or the <code>update-hook-configuration</code> command.</p>
    <p>This attribute can be abbreviated as <code>-c</code>.</p></td>
  </tr>
</table>

#### Sample command

To create a simple hook:

      razor create-hook --name myhook --hook-type some_hook --configuration foo=7



### `run-hook`

The `run-hook` command executes a hook with parameters you specify. This command is useful to test a hook you're writing, or to re-run a hook that failed.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>name</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the hook to run.</td>
  </tr>
  <tr>
    <td>event</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td><p>Name of the event to trigger the hook.</p>
     <p>For available event types see <a href="./razor_hooks.html#available-events">Available events</a>.</p></td>
  </tr>
  <tr>
    <td>node</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;"></td>
    <td>Name of the node you want to run the hook on.</td>
  </tr>
  <tr>
    <td>policy</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p>Name of the policy you want to use as input to the hook script when the hook runs.</p>
     <p>This attribute applies only to events that affect policies, for example <code>node-bound-to-policy</code> and <code>node-unbound-from-policy</code>.</p></td>
  </tr>
  <tr>
    <td>debug</td>
    <td style="text-align: center;"></td>
    <td style="text-align: center;"></td>
    <td><p><code>true</code> to include debug information in the event log.</p>
     <p>If omitted, debug information is not logged.</p></td>
  </tr>
</table>

#### Sample commands

To run a hook called `counter` when a node boots:

      razor run-hook --name counter --event node-booted --node node1



### `update-hook-configuration`

The `update-hook-configuration` command sets or clears a specified key value for a hook. This can be useful to iteratively test a hook you're writing. For example, you can write the hook, test it with `run-hook`, then modify the hook as needed with `update-hook-configuration`.

#### Command attributes

<table>
  <tr>
    <th>Attributes</th>
    <th style="text-align: center;">Required</th>
    <th style="text-align: center;">Positional arguments</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>hook</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">1</td>
    <td>Name of the hook to update.</td>
  </tr>
  <tr>
    <td>key</td>
    <td style="text-align: center;">✓</td>
    <td style="text-align: center;">2</td>
    <td>Name of the key to modify in the hook's configuration file.</td>
  </tr>
  <tr>
    <td>value</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;">3</td>
    <td>Value to change the key to.</td>
  </tr>
  <tr>
    <td>clear</td>
    <td style="text-align: center;">*</td>
    <td style="text-align: center;"></td>
    <td><code>true</code> to clear the value of the specified key.</td>
  </tr>
</table>
\* You must specify <code>value</code> or <code>clear</code>.

#### Sample command

To change the key `some_key` to `new_value`:

    razor update-hook-configuration --hook hook1 \
      --key my_key --value new_value



### `delete-hook`

The `delete-hook` command deletes a specified hook from the Razor database.

#### Command attributes

Attributes      | Required     | Positional arguments   | Description
----------------|:---------------:|:-------------------:|-------------------------
name            | ✓             | 1                     | Name of the hook to delete.

#### Sample commands

To delete an obsolete hook:

      razor delete-hook --name my_obsolete_hook





