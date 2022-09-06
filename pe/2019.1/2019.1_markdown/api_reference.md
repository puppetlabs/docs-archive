# Razor API reference

Use the Razor API to interact with Razor and provision bare metal nodes

**Related information**  


[API index](api_index.md#)

## The default bootstrap iPXE file

A GET request to `/api/microkernel/bootstrap` returns the iPXE script that you should put on your TFTP server \(usually called `bootstrap.ipxe`\). The script gathers information about the node \(the `hw_info`\) that it sends to the server and that the server uses to identify the node and determine how exactly the node boots.

The URL accepts the parameter `nic_max`, which you should set to the maximum number of network interfaces that respond to DHCP on any given node. It defaults to 4.

The URL also accepts an `http_port` parameter, which tells Razor which port its internal HTTP communications should use, and the `/svc` URLs must be available through that port. The default install uses 8150 for this.

## Configuration API

Razor configuration is pulled from a configuration file controlled by Puppet Enterprise. You can change configuration values in the console with class parameters of the `pe_razor` class.

**Note:** In order for configuration changes to take effect, you must restart the Razor service by running `service pe-razor-server restart` on the Razor server.

### View configuration \(`config`\)

The `config` endpoint displays details about your Razor configuration.

**Note:** Properties specified in the `api_config_blacklist` aren't returned by the `config` endpoint.

The endpoint handles a `GET` request to the collection URL specified in `/api`.

## Nodes API

These commands enable you to register a node, set a node's hardware information, remove a single node, or remove a node's associate with any policies and clear its `installed` flag.

### Register a node \(`register-node`\)

Register a node with Razor before it is discovered, and potentially provisioned.

In environments in which some nodes have been provisioned outside of the purview of Razor, this command offers a way to tell Razor that a node is valuable and not eligible for automatic reprovisioning. Such nodes are reprovisioned only if they are marked available with the `reinstall-node` command.

The `register-node` command allows you to perform the same registration that would happen when a new node checks in, but ahead of time. The `register-node` command uses the `installed` value to indicate that a node has already been installed, which signals to Razor that the node should be ignored, and Razor should act as if it had successfully installed that node.

In order for this command to be effective, `hw_info` must contain enough information that the node can successfully be identified based on the hardware information sent from iPXE when the node boots; this usually includes the MAC addresses of all network interfaces of the node.

`register-nodes` accepts the following parameters:

-   `hw_info` — Required, provides the hardware information for the node. This is used to match the node on first boot with the record in the database. The `hw_info` can contain all or a subset of the following entries:
    -   `netN` — The MAC addresses of each network interface, for example `net0` or `net2`: The order of the MAC addresses is not significant.
    -   `serial` — The DMI serial number of the node.
    -   `asset` — The DMI asset tag of the node.
    -   `uuid` — DMI UUID of the node.
-   `installed` — A boolean flag indicating whether this node should be considered installed and therefore not eligible for reprovisioning by Razor

**API example**

Registering a machine before booting it with `installed` set to `true` protects it from accidental reinstallation by Razor:

```
{
  "hw_info": {
    "net0":   "78:31:c1:be:c8:00",
    "net1":   "72:00:01:f2:13:f0",
    "net2":   "72:00:01:f2:13:f1"
  },
  "installed": true
}
```

### Set node hardware info \(`set-node-hw-info`\)

When a node's hardware changes, such as a network card being replaced, the Razor server needs to be informed so it can correctly match the new hardware to the existing node definition.

The `set-node-hw-info` command lets you replace the existing hardware data with new data, prior to booting the modified node on the network. For example, update node172 with new hardware information as follows:

```
  {
      "node": "node172",
      "hw_info": {
        "net0":   "78:31:c1:be:c8:00",
        "net1":   "72:00:01:f2:13:f0",
        "net2":   "72:00:01:f2:13:f1"
      }
  }
```

The format of the `hw_info` is the same as for the `register-node` command.

### Delete node \(`delete-node`\)

To remove a single node, provide its name:

```
{
  "name": "node17"
}
```

**Note:** If the deleted node boots again at some point, Razor automatically recreates it.

### Reinstall node \(`reinstall-node`\)

To remove a node's association with any policy and clear its `installed` flag, provide its name:

```
{
  "name": "node17"
}
```

Once the node reboots, it boots back into the microkernel, goes through discovery and tag matching, and can bind to another policy for reinstallation. This command does not change the node's metadata or facts.

## IPMI API

IPMI commands are node commands based on the Intelligent Platform Management Interface.

**Note:** You must install the `ipmitool` on the Razor server before using IPMI commands. To install the tool, run `yum install ipmitool -y`.

### Set node IPMI credentials \(`set-node-ipmi-credentials`\)

Razor can store IPMI credentials on a per-node basis. These credentials include a hostname \(or IP address\), username, and password to use when contacting the BMC/LOM/IPMI LAN or LANplus service to check or update power state and other node data.

After IPMI credentials have been set up for a node, you can use the `reboot-node` and `set-node-desired-power-state` commands.

These three data items must be set or reset together, in a single operation. When you omit a parameter, Razor sets it to `NULL` \(representing no value, or the `NULL` username/password as defined by IPMI\).

The structure of a request is:

```
{
  "name": "node17",
  "ipmi_hostname": "bmc17.example.com",
  "ipmi_username": null,
  "ipmi_password": "sekretskwirrl"
}
```

This command works only with remote IPMI targets, not locally; therefore, you *must* provide an IPMI hostname.

### Reboot node \(`reboot-node`\)

If you've associated IPMI credentials with a node, Razor can use IPMI to trigger a hard power cycle.

Just provide the name of the node:

```
{
  "name": "node1",
}
```

The IPMI communication spec includes some generous internal rate limits to prevent it from overwhelming the network or host server. If an execution slot isn't available on the target node, your `reboot-node` command goes into a background queue, and runs as soon as a slot is available.

This background queue is cumulative and persistent: there are no limits on how many commands you can queue up, how frequently a node can be rebooted, or how long a command can stay in the queue. If you restart your Razor server before the queued commands are executed, they remain in the queue and run after the server restarts.

The `reboot node` command is not integrated with IPMI power state monitoring, so you can't see power transitions in the record or when polling the node object.

### Set a node's desired power state \(`set-node-desired-power-state`\)

By default, Razor checks your nodes' power states every few minutes in the background. If it detects a node in a non-desired state, Razor issues an IPMI command directing the node to its desired state.

To set the desired state for a node:

```
{
  "name": "node1234",
  "to":   "on"|"off"|null
}
```

The `name` parameter identifies the node to change the setting on. The `to` parameter contains the desired power state to set. Valid values are `on`, `off`, or `null` \(the JSON NULL/nil value\), which reflect "power on", "power off", and "do not enforce power state" respectively.

## Node metadata API

These commands enable you to add, update, or remove metadata keys and remove metadata entries.

### Modify node metadata \(`modify-node-metadata`\)

Node metadata is a collection of key/value pairs, much like a node's facts. The difference is that the facts represent what the node tells Razor about itself, while its metadata represents what you tell Razor about the node.

The `modify-node-metadata` command lets you add, update, or remove individual metadata keys, or clear a node's metadata:

```
{
  "node": "node1",
  "update": {                         # Add or update these keys
      "key1": "value1",
      "key2": "value2",
        ...
  }
  "remove": [ "key3", "key4", ... ],  # Remove these keys
  "no_replace": true                  # Do not replace keys on
                                        # update. Only add new keys
}
```

or

```
{
  "node": "node1",
  "clear": true                       # Clear all metadata
}
```

You can submit multiple updates or removals in a single command. However, `clear` only works on its own.

**Tip:** In batch updates with `no_replace`, use `force` to bypass errors. Existing keys aren't modified.

### Update node metadata \(`update-node-metadata`\)

The `update-node-metadata` command offers a simplified way to update a single metadata key.

The body for the command must be:

```
{
    "node"      : "mode1",
    "key"       : "my_key",
    "value"     : "my_val",
    "no_replace": true
}
```

The `no_replace` parameter is optional. If it is `true`, the metadata entry is not modified if it already exists.

### Remove node metadata \(`remove-node-metadata`\)

The `remove-node-metadata` command offers a simplified way to remove either a single metadata entry or all metadata entries on a node:

```
{
  "node" : "node1",
  "key"  : "my_key",
}
```

or

```
{
  "node" : "node1",
  "all"  : true,     # Removes all keys
}
```

## Repositories API

These commands enable you to create a new repository or delete a repository from the internal Razor database. You can also ensure that a specified repository uses a specified task.

### Create new repository \(`create-repo`\)

The `create-repo` command creates a new repository. The repository can contain the content to install a node, or it can point to an existing online repository.

You can create three types of repositories:

-   Those that reference content available on another server, for example, on a mirror you maintain \(`url`\).
-   Those where Razor unpacks ISOs for you and serves their contents \(`iso_url`\).
-   Those where Razor creates a stub directory that you can manually fill with content \(`no_content`\).

The `task` parameter is mandatory in all three variants of this command, and indicates the default task to use when installing machines using this repository. The `task` parameter can be overridden at the policy level. If you're not using a task, reference the stock task `noop`.

To have Razor unpack an ISO for you and serve its content:

```
{
  "name": "fedora19",
  "iso_url": "file:///tmp/Fedora-19-x86_64-DVD.iso"
  "task": "puppet"
}
```

**Tip:** Supplying the `iso_url` property when you create a repository ensures that you can delete it from the server with the `delete-repo` command.

To create a repository that points to an existing resource without loading anything onto the Razor server:

```
{
  "name": "fedora19",
  "url": "http://mirrors.n-ix.net/fedora/linux/releases/19/Fedora/x86_64/os/"
  "task": "noop"
}
```

To create an empty directory that you can manually fill later:

```
{
  "name": "win2012r2",
  "no_content": true
  "task": "noop"
}
```

After creating a `no_content` repository, you can log into your Razor server and fill the repository directory with the content, for example by loopback-mounting the install media and copying it into the directory. The repository directory is specified by the `repo_store_root` class parameter of the `pe_razor` class. By default, the directory is in `/opt/puppetlabs/server/data/razor_server/repo`.

Using `no_content` is usually required for Windows install media, because the library that Razor uses to unpack ISO images can't handle Windows ISO images.

### Delete a repository \(`delete-repo`\)

The `delete-repo` command deletes a repository from the internal Razor database.

The command accepts a single repository name:

```
{
  "name": "fedora16"
}
```

This command deletes the repository from the internal Razor database. If you supplied the `iso_url` property when you created the repository, the folder is also deleted from the server. If you didn't supply the `iso_url` property, content remains in the repository directory.

### Update a repository's specified task \(`update-repo-task`\)

Ensures that a specified repository uses the task this command specifies, setting the task if necessary. If a node is currently provisioning against the repo when you run this command, provisioning might fail.

```
{
  "repo": "my_repo",
  "task": "other_task"
}
```

## Tasks API

This commands enables you to create a task in the Razor database.

**Important:** Razor tasks differ from Puppet tasks, which let you run arbitrary scripts and commands using Puppet. See the orchestrator documentation for information about Puppet tasks.

### Create task \(`create-task`\)

The `create-task` command creates a task in the Razor database. This command is an alternative to manually placing task files in the `task_path`. If you anticipate needing to make changes to tasks, we recommend the disk-backed task approach.

The body of the `POST` request for this command has the following form:

```
{
  "name": "redhat6",
  "os": "Red Hat Enterprise Linux",
  "boot_seq": {
    "1": "boot_install",
    "default": "boot_local"
  },
  "templates": {
    "boot_install": " ... ERB template for an ipxe boot file ...",
    "installer": " ... another ERB template ..."
  }
}
```

The possible properties in the request are:

-   `name` — The name of the task; must be unique.
-   `os` — The name of the OS; mandatory.
-   `description` — Human-readable description.
-   `boot_seq` — A hash mapping the boot counter or 'default' to an ERB template.
-   `templates` — A hash mapping template names to the actual ERB template text.

## Tags API

These commands enable you to create a tag, delete a tag, or change the rule for a tag.

### Create tag \(`create-tag`\)

To create a tag, use the following in the body of your `POST` request:

```
{
  "name": "small",
  "rule": ["=", ["fact", "processorcount"], "2"]
}
```

The `name` of the tag must be unique; the `rule` is a match expression.

### Delete tag \(`delete-tag`\)

To delete a tag, use the following in the body of your `POST` request:

```
{
  "name": "small",
  "force": true
}
```

You can't delete a tag while it's being used by a policy, unless you set the optional `force` parameter to `true`. In that case, Razor removes the tag from all policies using it and then deletes it.

### Update tag \(`update-tag-rule`\)

To change the rule for a tag, use the following in the body of your `POST` request:

```
{
  "name": "small",
  "rule": ["<=", ["fact", "processorcount"], "2"],
  "force": true
}
```

This changes the rule of the given tag to the new rule. Razor then reevaluates the tag against all nodes and updates each node's tag attribute to reflect whether the tag now matches or not.

If the tag is used by any policies, the update is performed only if you set the optional `force` parameter to `true`. Otherwise, the command returns status code 400.

## Policies API

Policies govern how nodes are provisioned depending on how they are tagged.

Razor maintains an ordered table of policies. When a node boots, Razor traverses this table to find the first eligible policy for that node. A policy might be ineligible for binding to a node if the node does not contain all of the tags on the policy, if the policy is disabled, or if the policy has reached its maximum for the number of allowed nodes.

When you list the `policies` collection, the list is in the order in which Razor checks policies against nodes.

### Create policy \(`create-policy`\)

```
{
  "name": "a policy",
  "repo": "some_repo",
  "task": "redhat6",
  "broker": "puppet",
  "hostname": "host${id}.example.com",
  "root_password": "secret",
  "max_count": 20,
  "before"|"after": "other policy",
  "node_metadata": { "key1": "value1", "key2": "value2" },
  "tags": ["existing_tag", "another_tag"]
}
```

**Note:** Because the policy contains many fields, you might want to put it in a JSON file. If you do, then your `create-policy` command would include the file name, like this: `razor create-policy --json <name of policy file>.json`.

Tags, repos, tasks, and brokers are referenced by name. The `tags` are optional. A policy with no tags can be applied to a node. The `task` is also optional. If it is omitted, the `repo`'s task is used. The `repo` and `broker` entries are required.

The `hostname` parameter defines a simple pattern for the hostnames of nodes bound to your policy. The `${id}` references each node's DB id.

The `max_count` parameter sets an upper limit on how many nodes can be bound to your policy at a time. You can specify a positive integer, or make it unlimited by setting it to `nil`.

Razor considers each policy sequentially, based on its order in a table. By default, new policies go at the end of the table. To override the default order, include a `before` or `after` argument referencing an existing policy by name.

The `node_metadata` parameter lets your policy apply metadata to a node when it binds. This does not overwrite existing metadata; it only adds keys that are missing. To install Windows on non-English systems, specify the `win_language` using the culture code for the appropriate [Microsoft language pack](https://technet.microsoft.com/en-us/library/cc722435(v=ws.10).aspx)\). For example, `--node-metadata win_language=es-ES`.

### Move policy \(`move-policy`\)

This command lets you change the order in which Razor considers your policies for matching against nodes.

To move an existing policy into a different place in the order, use the `move-policy` command with a body like:

```
{
  "name": "a policy",
  "before"|"after": "other policy"
}
```

This changes the policy table so that "a policy" appears before or after "other policy".

### Enable/disable policy \(`enable-policy/disable-policy`\)

To keep a policy from being matched against any nodes, disable it with the `disable-policy` command.

To enable a disabled policy, use the `enable-policy` command. Both commands use a body like:

```
{
  "name": "a policy"
}
```

### Modify the `max_count` for a policy \(`modify-policy-max-count`\)

The command `modify-policy-max-count` lets you set the maximum number of nodes that can be bound to a specific policy.

The body of the request should be of the form:

```
{
  "name": "a policy"
  "max_count": new-count
}
```

`new-count` can be an integer, which must be greater than the number of nodes that are currently bound to the policy. Alternatively, the `no_max_count` argument makes the policy unbounded:

```
{
  "name": "a policy"
  "no_max_count": true
}
```

### Add tags to policy \(`add-policy-tag`\)

To add tags to a policy, supply the name of a policy and of the tag:

```
{
  "name": "a-policy-name",
  "tag" : "a-tag-name",
}
```

To create the tag in addition to adding it to the policy, supply the `rule` argument:

```
{
  "name": "a-policy-name",
  "tag" : "a-new-tag-name",
  "rule": "new-match-expression"
}
```

### Remove tags from policy \(`remove-policy-tag`\)

To remove tags from a policy, supply the name of a policy and the name of the tag.

```
{
  "name": "a-policy-name",
  "tag" : "a-tag-name",
}
```

A policy with no tags can still be applied to any node.

### Update a policy's specified repository \(`update-policy-repo`\)

Ensures that a policy uses the repository this command specifies. If necessary, `update-policy-repo` sets the repository, for example if a policy has already been created and you want to add a repository to it.

The following shows how to update a policy's repository to a repository called "fedora21":

```
{
  "node": "node1",
  "policy": "my_policy",
  "repo": "fedora21"
}
```

### Update a policy's specified task \(`update-policy-task`\)

Ensures that a policy uses the task this command specifies. If necessary, `update-policy-task` sets the task, for example if a policy has already been created and you want to add a task to it.

If a node is currently provisioning against the policy when you run this command, provisioning can fail.

The following shows how to update a policy's task to a task called "other\_task".

```
{
  "node": "node1",
  "policy": "my_policy",
  "task": "other_task"
}
```

### Update a policy's specified broker \(`update-policy-broker`\)

Ensures that a policy uses the broker this command specifies. If necessary, `update-policy-broker` sets the broker, for example if a policy has already been created and you want to add a broker to it.

If a node is currently provisioning against the policy when you run this command, provisioning can fail.

The following shows how to update a policy's broker to a broker called "legacy-puppet":

```
{
  "node": "node1",
  "policy": "my_policy",
  "broker": "legacy-puppet"
}
```

### Update a policy's node metadata \(`update-policy-node-metadata`\)

Ensures that a policy uses the node metadata this command specifies. If necessary, `update-policy-node-metadata` sets the node metadata, for example if a policy has already been created and you want to add node metadata to it.

The following shows how to update a policy's node metadata for the "my\_key" value:

```
{
  "node": "node1",
  "policy": "my_policy",
  "key": "my_key"
  "value": "my_value"
}
```

### Delete policy \(`delete-policy`\)

To delete a policy, supply the name of a single policy:

```
{
  "name": "my-policy"
}
```

Note that this does not affect the `installed` status of a node, and therefore can't, by itself, make a node bind to another policy upon reboot.

## Brokers API

A broker is responsible for configuring a newly installed node for a specific configuration management system. For Puppet Enterprise, you generally use brokers only with the type `puppet-pe`.

### Create broker \(`create-broker`\)

To create a broker, post the following to the `create-broker` URL:

```
{
  "name": "puppet",
  "configuration": {
     "server": "puppet.example.org"
  },
  "broker-type": "puppet-pe"
}
```

The `broker-type` must correspond to a broker that is present in a valid broker directory. Broker directories are specified in the `broker_path` class parameter of the `pe_razor` class. By default, the broker path for custom brokers is `/etc/puppetlabs/razor-server/brokers:brokers`.

The permissible settings for the `configuration` hash depend on the broker type and are declared in the broker type's `configuration.yaml`. For the `puppet-pe` broker type, these are:

-   `server` — The hostname of the master.
-   `version` — The agent version to install; this defaults to `current` and should be used only in exceptional circumstances.
-   `ntpdate_server` — URL for an NTP server, such as us.pool.ntp.org, used to synchronize the date and time before installing the agent.

### Update broker configuration \(`update-broker-configuration`\)

To set or clear a specified key value for a broker, use `update-broker-configuration`.

This argument changes the key "some\_key" to "new\_value":

```
{
  "broker": "mybroker",
  "key": "some_key",
  "value": "new_value"
}
```

This argument clears the value for "some\_key":

```
{
  "broker": "mybroker",
  "key": "some_key",
  "clear": true
}
```

The `update-broker-configuration` command can be useful to update the Puppet Enterprise version of a `puppet-pe` broker. For example, if you created a `puppet-pe` broker for version 2015.2.0, you can update it with:

```
{
  "broker": "puppet-pe",
  "key": "version",
  "value": "2015.3.0"
}
```

### Delete broker \(`delete-broker`\)

The `delete-broker` command requires only the name of the broker:

```
{
  "name": "small",
}
```

It is not possible to delete a broker that is used by a policy.

## Hooks API

Hooks are custom, executable scripts that are triggered to run when a node hits certain phases in its lifecycle. A hook script receives several properties as input, and can make changes in the node’s metadata or the hook’s internal configuration.

Hooks can be useful for:

-   Notifying an external system about the stage of a node’s installation.
-   Querying external systems for information that modifies how a node gets installed.
-   Calculating complex values for use in a node’s installation configuration.

### Create hook \(`create-hook`\)

To create a new hook, use the `create-hook` command with a body like this:

```
{
  "name": "myhook",
  "hook_type": "some_hook",
  "configuration": {"foo": 7, "bar": "rhubarb"}
}
```

The `hook_type` must correspond to a hook with the specified name in a valid hook directory. Hook directories are specified in the `hook_path` class parameter of the `pe_razor` class. By default, the hook directory for custom hooks is `/etc/puppetlabs/razor-server/hooks:hooks`.

The optional `configuration` parameter lets you provide a starting configuration corresponding to that `hook_type`.

### Update hook configuration \(`update-hook-configuration`\)

To set or clear a specified key value for a hook, use `update-hook-configuration`.

This argument changes the key "some\_key" to "new\_value":

```
{
  "hook": "myhook",
  "key": "some_key",
  "value": "new_value"
}
```

This argument clears the value for "some\_key":

```
{
  "hook": "myhook",
  "key": "some_key",
  "clear": true
}
```

### Delete hook \(`delete-hook`\)

To delete a single hook, provide its name:

```
{
  "name": "my-hook"
}
```

