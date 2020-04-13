---
author: melissa amos <melissa.amos@puppet.com\>
---

# Hooks

Hooks are an optional but useful Razor object. Hooks provide a way to run arbitrary scripts when certain events occur during a node's lifecycle. The behavior and structure of a hook are defined by a *hook type*.

The two primary components for hooks are:

-   **Configuration** — A JSON document for storing data on a hook. Configurations have an initial value and can be updated by hook scripts.
-   **Event scripts** — Scripts that run when a specified event occurs. Event scripts must be named according to the handled event.

## Hook storage directories

There are two directories that store hooks:

-   `/opt/puppetlabs/server/apps/razor-server/share/razor-server/hooks` stores default hooks shipped with the product.
-   `/etc/puppetlabs/razor-server/hooks` stores custom hooks.

**Note:** Don't modify the directory or hooks at `/opt...`, but you can copy hooks from there to the custom hook directory and modify them as needed.

## File layout for a hook type

Similar to brokers and tasks, hook types are defined through a `.hook` directory and optional event scripts within that directory.

```
    hooks/
      some.hook/
        configuration.yaml
        node-bind-policy
        node-unbind-policy
        ...
```

## Available events

These are the events that you can create hooks for.

-   `node-booted` — Triggered every time a node boots via iPXE.
-   `node-registered` — Triggered after a node has been registered. Limited hardware information is available after registration.
-   `node-deleted` — Triggered after a node has been deleted.
-   `node-bound-to-policy` — Triggered after a node has been bound to a policy. The script input contains a `policy` property with the details of the policy that has been bound to the node.
-   `node-unbound-from-policy` — Triggered after a node has been marked as uninstalled by the `reinstall-node` command and thus has been returned to the set of nodes available for installation.
-   `node-facts-changed`— Triggered whenever a node changes its facts.
-   `node-install-finished` — Triggered when a policy finishes its last step.

## Creating hooks

The `create-hook` command is used to create a hook object from a hook type. For example:

```
razor create-hook --name myhook --hook-type some_hook
    --configuration example1=7 --configuration example2=rhubarb
```

The hook object created by this command sets its initial configuration to the JSON document:

```
{
	"example1": 7,
    "example2": "rhubarb"
}
```

Each time an event script for a hook runs, it has an opportunity to modify the hook's configuration. These changes to the configuration are preserved by the Razor server. The server also makes sure that hooks don't modify their configurations concurrently in order to avoid data corruption.

The `delete-hook` command is used to remove a hook.

## Hook configuration

Hook scripts can use the hook object's `configuration`.

The hook type specifies the configuration data that it accepts in `configuration.yaml`. That file must define a hash:

```
example1:
  description: "Explain what example1 is for"
  default: 0
example2:
  description "Explain what example2 is for"
  default: "Barbara"
...
```

For each event that the hook type handles, it must contain a script with the event's name. That script must be executable by the Razor server. All hook scripts for a certain event are run in an indeterminate order when that event occurs.

## Event scripts

The general protocol is that hook event scripts receive a JSON object on their `stdin`, and might return a result by printing a JSON object to their `stdout`.

The properties of the input object vary by event, but they always contain a `hook` property:

```
{
  "hook": {
    "name": hook name,
    "configuration": ... operations to perform ...
  }
}
```

The `configuration` object is initialized from the hash described in the hook's `configuration.yaml` and the properties set by the current values of the hook object's `configuration`. With the `create-hook` command above, the input JSON would be:

```
{
  "hook": {
    "name": "myhook",
    "configuration": {
      "update": {
        "example1": 7,
        "example2": "rhubarb"
      }
    }
  }
}
```

The script might return data by producing a JSON object on its `stdout` to indicate changes that need to be made to the hook's `configuration`. The updated `configuration` is used on subsequent invocations of any event for that hook. The output must indicate which properties to update, and which ones to remove:

```
{
  "hook": {
    "configuration": {
      "update": {
        "example1": 8
      },
      "remove": [ "frob" ]
    }
  }
}
```

The Razor server ensures that invocations of hook scripts are serialized. For any hook, events are processed one-by-one to allow for transactional safety around the changes any event script might make.

### Node events

Most events are directly related to a node. The JSON input to the event script has a `node` property that contains the representation of the node in the same format the API produces for node details.

The JSON output of the event script can modify the node metadata:

```
{
  "node": {
    "metadata": {
      "update": {
        "example1": 8
      },
      "remove": [ "frob" ]
    }

```

### Error handling

The hook script must exit with exit code 0 if it succeeds; any other exit code is considered a failure of the script.

Whether the failure of a script has any other effects depends on the event. A failed execution can still make updates to the hook and node objects by printing to `stdout` in the same way as a successful execution.

To report error details, the script produces a JSON object with an `error` property on its `stdout` in addition to exiting with a non-zero exit code. If the script exits with exit code 0, the `error` property is still recorded, but the event's severity isn't an error. The `error` property itself contains an object whose `message` property is a human-readable message; additional properties can be set. For example:

```
{
  "error": {
    "message": "connection refused by frobnicate.example.com",
    "port": 2345,
        ...
    ...
  }
}
```

### Sample input

The input to the hook script is in JSON, containing a structure like this:

```
{
  "hook": {
    "name": "counter",
    "configuration": { "value": 0 }
  },
  "node": {
    "name": "node10",
    "hw_info": {
      "mac": [ "52-54-00-30-8e-45" ],
      ...
    },
    "dhcp_mac": "52-54-00-30-8e-45",
    "tags": ["compute", "anything", "any", "new"],
    "facts": {
      "memorysize_mb": "995.05",
      "facterversion": "2.0.1",
      "architecture": "x86_64",
      ...
    },
    "state": {
      "installed": false
      "physicalprocessorcount": "1",
    },
    "hostname": "client-l.watzmann.net",
    "root_password": "secret",
    "netmask_eth0": "255.255.255.0",
    "ipaddress_lo": "127.0.0.1",
    "last_checkin": "2014-05-21T03:45:47+02:00"
  },
  "policy": {
    "name": "client-l",
    "repo": "centos-6.7",
    "task": "ubuntu",
    "broker": "noop",
    "enabled": true,
    "hostname_pattern": "client-l.watzmann.net",
    "root_password": "secret",
    "tags": ["client-l"],
    "nodes": { "count": 0 }
  }
}
```

### Sample hook

Here is an example of a basic hook called `counter` that counts the number of times Razor registers a node.

This example creates a corresponding directory for the hook type, `counter.hook`, inside the `hooks` directory. You can store the current count as a configuration entry with the key `count`. Thus the `configuration.yaml` file might look like this:

```
count:
  description: "The current value of the counter"
  default: 0
```

To make sure a script runs whenever a node is bound to a policy, create a file called `node-bound-to-policy` and place it in the `counter.hook` folder. Then write this script, which reads in the current configuration value, increments it, then returns some JSON to update the configuration on the hook object:

```

#! /bin/bash

json=$(< /dev/stdin)

name=$(jq '.hook.name' <<< $json)
value=$(( $(jq '.hook.config.count' <<< $json) + 1 ))

cat <<EOF
{
  "hook": {
    "configuration": {
      "update": {
        "count": $value
      }
    }
  },
  "node": {
    "metadata": {
      $name: $value
    }
  }
}
EOF
```

Note that this script uses `jq`, a bash JSON manipulation framework. This must be on the `$PATH` in order for execution to succeed.

Next, create the hook object, which stores the configuration:

```
razor create-hook --name counter --hook-type counter
```

Because the configuration is absent from this creation call, the default value of 0 in `configuration.yaml` is used. Alternatively, this could be set using `--configuration count=0` or `--c count=0`.

The hook is now ready to use. You can query the existing hooks in a system via `razor hooks`. To query the current value of the hook's configuration, `razor hooks counter` shows `count` initially set to 0. When a node gets bound to a policy, the `node-bound-to-policy` script is triggered, yielding a new configuration value of 1.

## Assign dynamic hostnames using hooks

You can use a hook to create more advanced dynamic hostnames than the simple incremented pattern — `$\{id\}.example.com` — from the `hostname` property on a policy.

### Before you begin

Ruby must be installed in `$PATH` for the hook script to succeed. By default, Ruby is installed as required with Puppet Enterprise. If Ruby isn't installed, add it to one of the paths specified in the `hook_execution_path` class parameter of the `pe_razor` class.

### About this task

This type of hook calculates the correct hostname and returns that hostname as metadata on the node. To do so, it uses a basic counter system that stores the number of nodes bound to a given policy.

This hook is intended to be extended for cases where an external system needs to be contacted to determine the correct hostname. In such a scenario, the new value is still returned as metadata for the node.

### Procedure

1.  Create an instance of a default hook:

    ```
    razor create-hook --name some_policy_hook --hook-type hostname \
        --configuration policy=some_policy \
        --configuration hostname-pattern='${policy}${count}.example.com'
    ```

2.  \(Optional\) If multiple policies require their own counter, create multiple instances of this hook with different `policy` or `hostname-pattern` hook configurations.


### Results

Running the `create-hook` command kicks off this sequence of events:

1.  The counter for the policy starts at 1.
2.  When a node boots, the `node-bound-to-policy` event is triggered.
3.  The policy's name from the event is then passed to the hook as input.
4.  The hook matches the node's policy name to the hook's policy name.
5.  If the policy matches, the hook calculates a rendered `hostname-pattern`:

    -   It replaces `${count}` with the current value of the `counter` hook configuration.
    -   It left-pads the `${count}` with `padding` zeroes. For example, if the hook configuration's `padding` equals 3, a `count` of 7 is rendered as `007`.
    -   It replaces `${policy}` with the name of its policy.
6.  The hook returns the rendered `hostname-pattern` as the node metadata of `hostname` and returns the incremented value for the counter that was used, so that the next execution of the hook uses the next value.

**Viewing the hook's activity log**

To view the status of the hook's executions, see `razor hooks $name log`:

```
    timestamp: 2015-04-01T00:00:00-07:00
       policy: policy_name
        cause: node-bound-to-policy
  exit_status: 0
     severity: info
      actions: updating hook configuration: {"update"=>{"counter"=>2}} and updating node metadata: {"update"=>{"hostname"=>"policy_name1.example.com"}}
```

**Related information**  


[Hook commands](using_the_razor_client.md#)

[Hooks API](api_reference.md#)

