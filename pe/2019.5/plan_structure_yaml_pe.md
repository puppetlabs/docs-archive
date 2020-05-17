---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Plan structure

YAML plans contain a list of steps with optional parameters and results.

YAML maps accept these keys:

-   `steps`: The list of steps to perform

-   `parameters`: \(Optional\) The parameters accepted by the plan

-   `return`: \(Optional\) The value to return from the plan


## Steps key

The `steps` key is an array of step objects, each of which corresponds to a specific action to take.

When the plan runs, each step is executed in order. If a step fails, the plan halts execution and raises an error containing the result of the step that failed.

Steps use these fields:

-   `name`: A unique name that can be used to refer to the result of the step later

-   `description`: \(Optional\) An explanation of what the step is doing


Other available keys depend on the type of step.

### Command step

Use a `command` step to run a single command on a list of targets and save the results, containing stdout, stderr, and exit code.

The step fails if the exit code of any command is non-zero.

Command steps use these fields:

-   `command`: The command to run

-   `target`: A target or list of targets to run the command on


For example:

```
steps:
  - command: hostname -f
    target:
      - web1.example.com
      - web2.example.com
      - web3.example.com
    description: "Get the webserver hostnames"
```

### Task step

Use a `task` step to run a Bolt task on a list of targets and save the results.

Task steps use these fields:

-   `task`: The task to run
-   `target`: A target or list of targets to run the task on
-   `parameters`: \(Optional\) A map of parameter values to pass to the task

For example:

```
steps:
  - task: package
    target:
      - web1.example.com
      - web2.example.com
      - web3.example.com
    description: "Check the version of the openssl package on the webservers"
    parameters:
      action: status
      name: openssl
```

### Script step

Use a `script` step to run a script on a list of targets and save the results.

The script must be in the `files/` directory of a module. The name of the script must be specified as `<modulename>/path/to/script`, omitting the `files` directory from the path.

Script steps use these fields:

-   `script`: The script to run
-   `target`: A target or list of targets to run the script on
-   `arguments`: \(Optional\) An array of command-line arguments to pass to the script

For example:

```
steps:
  - script: mymodule/check_server.sh
    target:
      - web1.example.com
      - web2.example.com
      - web3.example.com
    description: "Run mymodule/files/check_server.sh on the webservers"
    arguments:
      - "/index.html"
      - 60
```

### File upload step

Use a file upload step to upload a file to a specific location on a list of targets.

The file to upload must be in the `files/` directory of a Puppet module. The source for the file must be specified as `<modulename>/path/to/file`, omitting the `files` directory from the path.

File upload steps use these fields:

-   `source`: The location of the file to be uploaded
-   `destination`: The location to upload the file to

For example:

```
steps:
  - source: mymodule/motd.txt
    destination: /etc/motd
    target:
      - web1.example.com
      - web2.example.com
      - web3.example.com
    description: "Upload motd to the webservers"
```

### Plan step

Use a `plan` step to run another plan and save its result.

Plan steps use these fields:

-   `plan`: The name of the plan to run
-   `parameters`: \(Optional\) A map of parameter values to pass to the plan

For example:

```
steps:
  - plan: facts
    description: "Gather facts for the webservers using the built-in facts plan"
    parameters:
      nodes:
        - web1.example.com
        - web2.example.com
        - web3.example.com
```

### Resources step

Use a `resources` step to apply a list of Puppet resources. A resource defines the desired state for part of a target. Bolt ensures each resource is in its desired state. Like the steps in a `plan`, if any resource in the list fails, the rest are skipped.

For each `resources` step, Bolt executes the `apply_prep` plan function against the targets specified with the `targets` field. For more information about `apply_prep` see the Applying manifest block section.

Resources steps use these fields:

-   `resouces`: An array of resources to apply
-   `target`: A target or list of targets to apply the resources on

Each resource is a YAML map with a type and title, and optionally a `parameters` key. The resource type and title can either be specified separately with the `type` and `title` keys, or can be specified in a single line by using the type name as a key with the title as its value.

For example:

```
steps:
  - resources:
    # This resource is type 'package' and title 'nginx'
    - package: nginx
      parameters:
        ensure: latest
    # This resource is type 'service' and title 'nginx'
    - type: service
      title: nginx
      parameters:
        ensure: running
    target:
      - web1.example.com
      - web2.example.com
      - web3.example.com
    description: "Set up nginx on the webservers"
```

## Parameters key

Plans accept parameters with the `parameters` key. The value of `parameters` is a map, where each key is the name of a parameter and the value is a map describing the parameter.

Parameter values can be referenced from steps as variables.

Parameters use these fields:

-   `type`: \(Optional\) A valid [Puppet data type](https://puppet.com/docs/puppet/latest/lang_data.html#puppet-data-types). The value supplied must match the type or the plan fails.

-   `default`: \(Optional\) Used if no value is given for the parameter

-   `description`: \(Optional\)


For example, this plan accepts a `load_balancer` name as a string, two sets of nodes called `frontends` and `backends`, and a `version` string:

```
parameters:
  # A simple parameter definition doesn't need a type or description
  load_balancer:
  frontends:
    type: TargetSpec
    description: "The frontend web servers"
backends:
    type: TargetSpec
    description: "The backend application servers"
  version:
    type: String
              description: "The new application version to deploy"
```

## How strings are evaluated

The behavior of strings is defined by how they're written in the plan.

`'single-quoted strings'` are treated as string literals without any interpolation.

`"double-quoted strings"` are treated as Puppet language double-quoted strings with variable interpolation.

`| block-style strings` are treated as expressions of arbitrary Puppet code. Note the string itself must be on a new line after the `|` character.

`bare strings` are treated dynamically based on their content. If they begin with a `$`, they're treated as Puppet code expressions. Otherwise, they're treated as YAML literals.

Here's an example of different kinds of strings in use:

```
parameters:
  message:
    type: String
    default: "hello"

steps:
  - eval: hello
    description: 'This will evaluate to: hello'
  - eval: $message
    description: 'This will evaluate to: hello'
  - eval: '$message'
    description: 'This will evaluate to: $message'
  - eval: "${message} world"
    description: 'This will evaluate to: hello world'
  - eval: |
      [$message, $message, $message].join(" ")
    description: 'This will evaluate to: hello hello hello'
```

