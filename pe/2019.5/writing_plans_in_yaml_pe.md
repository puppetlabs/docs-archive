---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Writing plans in YAML

YAML plans run a list of steps in order, which allows you to define simple workflows. Steps can contain embedded Puppet code expressions to add logic where necessary.

**Note:** YAML plans are an experimental feature and might experience breaking changes in future minor releases.

## Naming plans

It is important to name plans correctly according to the module name, file name, and path to ensure easy code readability.

Place plan files in your module's `./plans` directory, using these file extensions:

-   Puppet plans — `.pp`

-   YAML plans — `.yaml`, not `.yml`


Plan names are composed of two or more name segments, indicating:

-   The name of the module the plan is located in.

-   The name of the plan file, without the extension.

-   The path within the module, if the plan is in a subdirectory of `./plans`.


For example, given a module called `mymodule` with a plan defined in `./mymodule/plans/myplan.pp`, the plan name is `mymodule::myplan`.

A plan defined in `./mymodule/plans/service/myplan.pp`would be `mymodule::service::myplan`. This name is how you refer to the plan when you run commands.

The plan filename `init` is special: the plan it defines is referenced using the module name only. For example, in a module called `mymodule`, the plan defined in `init.pp` is the `mymodule` plan.

Avoid giving plans the same names as constructs in the Puppet language. Although plans do not share their namespace with other language constructs, giving plans these names makes your code difficult to read.

Each plan name segment must begin with a lowercase letter and:

-   May include lowercase letters.

-   May include digits.

-   May include underscores.

-   Must not be a [reserved word](https://docs.puppet.com/puppet/5.3/lang_reserved.html).

-   Must not have the same name as any Puppet data types.

-   Namespace segments must match the following regular expression `\A[a-z][a-z0-9_]*\Z`


## RBAC considerations for writing plans

Take user permissions into consideration when writing plans by understanding how RBAC for plans works.

RBAC for plans is separate from RBAC for individual tasks. This means that a user can be excluded from running a certain task, but still have permission to run a plan that contains that task. Setting one permission does not affect the other.

This structure allows you to write plans with more robust, custom control over task permissions. Instead of allowing a user free rein to run a task that can potentially damage your infrastructure under the right conditions, you can wrap a task in a plan and only allow them to run it under circumstances you can control.

For example, say you are configuring permissions for a new user and allow them to run the plan `plan infra::upgrade_git`:

```
plan infra::upgrade_git (
  TargetSpec $nodes,
  Integer $version,
) {
  run_task(‘package’, $nodes, name => ’git’, action => ‘upgrade’, version => $version)
}
```

Within this plan, they can run the `package` task, but can only interact with the `git` package. The plan does not allow them to use any other parameters for the `package` task.

Even though they can run this plan, they do not have access to individually run the `package` task outside of this plan unless you grant them permission to do so. In that case, they would have the option to add any parameters they want to the task.

### Use parameter types to fine-tune access

Writing parameter types into plan code provides even more control. In the `upgrade_git` example above, the plan only provides access to the `git` package, but the user can choose whatever version of `git` they want.

Let's say there are known vulnerabilities in some versions of the `git` package and you are concerned with your new user having the ability to use the versions you deem unsafe. You can use parameter types like Enum to restrict the version parameter to versions that are safe enough for deployment.

In this example, the Enum restricts the `$version` parameter to versions 1:2.17.0-1ubuntu1 and 1:2.17.1-1ubuntu0.4 only:

```
plan infra::upgrade_git (
  TargetSpec $nodes,
  Enum['1:2.17.0-1ubuntu1', '1:2.17.1-1ubuntu0.4'] $version,
) {
  run_task(‘package’, $nodes, name => ‘git’, action => ‘upgrade’, version => $version)
}
```

Any user attempting to run this plan must choose one of these versions for the plan to run.

You can also use PuppetDB queries to select parameter types. Using the same example, let's say you need to restrict the nodes that `infra::upgrade_git` can run on. Use a PuppetDB query to identify which nodes get selected for the git upgrade. It should look something like this:

```
plan infra::upgrade_git (
   Enum['1:2.17.0-1ubuntu1', '1:2.17.1-1ubuntu0.4'] $version,
) {
  # Use puppetdb to find the nodes from the “other” team's web cluster
  $query = [from, nodes, ['=', [fact, cluster], "other_team"]]
  $selected_nodes = puppetdb_query($query).map() |$target| {
    $target[certname]
  }
  run_task(‘package’, $selected_nodes, name => ‘git’, action => ‘upgrade’, version => $version)
}
```

Using these ideas, you can write powerful plans that give users exactly what they need without giving them the keys to the kingdom.

## Using variables and simple expressions

The simplest way to use a variable is to reference it directly by name. For example, this plan takes a parameter called `nodes` and passes it as the target list to a step:

```
parameters:
  nodes:
    type: TargetSpec

steps:
  - command: hostname -f
    target: $nodes
```

Variables can also be interpolated into string values. The string must be double-quoted to allow interpolation. For example:

```
parameters:
  username:
    type: String

steps:
  - task: echo
    message: "hello ${username}"
           target: $nodes
```

Many operations can be performed on variables to compute new values for step parameters or other fields.

### Indexing arrays or hashes

You can retrieve a value from an Array or a Hash using the `[]` operator. This operator can also be used when interpolating a value inside a string.

```
parameters:
  users:
    # Array[String] is a Puppet data type representing an array of strings
    type: Array[String]

steps:
  - task: user::add
    target: 'host.example.com'
    parameters:
      name: $users[0]
  - task: echo
    target: 'host.example.com'
    parameters:
      message: "hello ${users[0]}"
```

### Calling functions

You can call a built-in [Bolt function](https://puppet.com/docs/bolt/latest/plan_functions.html#bolt-functions) or [Puppet function](https://puppet.com/docs/puppet/latest/function.html) to compute a value.

```
parameters:
  users:
    type: Array[String]

steps:
  - task: user::add
    parameters:
      name: $users.first
  - task: echo
    message: "hello ${users.join(',')}"
```

### Using code blocks

Some Puppet functions take a block of code as an argument. For instance, you can filter an array of items based on the result of a block of code.

The result of the `filter` function is an array here, not a string, because the expression isn't inside quotes

```
parameters:
  numbers:
    type: Array[Integer]

steps:
  - task: sum
    description: "add up the numbers > 5"
    parameters:
      indexes: $numbers.filter |$num| { $num > 5 }
```

## Connecting steps

You can connect multiple steps by using the result of one step to compute the parameters for another step.

### `name` key

The `name` key makes its results available to later steps in a variable with that name.

This example uses the `map` function to get the value of `stdout` from each command result and then joins them into a single string separated by commas.

```
parameters:
  nodes:
    type: TargetSpec

steps:
  - name: hostnames
    command: hostname -f
    target: $nodes
  - task: echo
    parameters:
      message: $hostnames.map |$hostname_result| { $hostname_result['stdout'] }.join(',')
```

### `eval` step

The `eval` step evaluates an expression and saves the result in a variable. This is useful to compute a variable to use multiple times later.

```
parameters:
  count:
    type: Integer

steps:
  - name: double_count
    eval: $count * 2
  - task: echo
    target: web1.example.com
    parameters:
      message: "The count is ${count}, and twice the count is ${double_count}"
```

## Returning results

You can return a result from a plan by setting the `return` key at the top level of the plan. When the plan finishes, the `return` key is evaluated and returned as the result of the plan. If no `return` key is set, the plan returns `undef`

```
steps:
  - name: hostnames
    command: hostname -f
    target: $nodes

return: $hostnames.map |$hostname_result| { $hostname_result['stdout'] }
```

## Computing complex values

To compute complex values, you can use a Puppet code expression as the value of any field of a step except the `name`.

Bolt loads the plan as a YAML data structure. As it executes each step, it evaluates any expressions embedded in the step. Each plan parameter and the values of every previous named step are available in scope.

This lets you take advantage of the power of Puppet language in the places it's necessary, while keeping the rest of your plan simple.

When your plans need more sophisticated control flow or error handling beyond running a list of steps in order, it's time to convert them to [Puppet Language plans](writing_plans_in_puppet_language_pe.md#).

## Converting YAML plans to Puppet language plans

You can convert a YAML plan to a Puppet language plan with the `bolt plan convert` command.

```
bolt plan convert path/to/my/plan.yaml
```

This command takes the relative or absolute path to the YAML plan to be converted and prints the converted Puppet language plan to stdout.

**Note:** Converting a YAML plan might result in a Puppet plan which is syntactically correct, but behaves differently. Always manually verify a converted Puppet language plan's functionality. There are some constructs that do not translate from YAML plans to Puppet language plans. These are listed \[TODO: insert link to section below!\] below. If you convert a YAML plan to Puppet and it changes behavior, [file an issue](https://github.com/puppetlabs/bolt/issues) in Bolt's Git repo.

For example, with this YAML plan:

```
# site-modules/mymodule/plans/yamlplan.yaml
parameters:
  nodes:
    type: TargetSpec
steps:
  - name: run_task
    task: sample
    target: $nodes
    parameters:
      message: "hello world"
return: $run_task
```

Run the following conversion:

```
$ bolt plan convert site-modules/mymodule/plans/yamlplan.yaml
# WARNING: This is an autogenerated plan. It may not behave as expected.
plan mymodule::yamlplan(
  TargetSpec $nodes
) {
  $run_task = run_task('sample', $nodes, {'message' => "hello world"})
  return $run_task
}
```

## Quirks when converting YAML plans to Puppet language

There are some quirks and limitations associated with converting a plan expressed in YAML to a plan expressed in the Puppet language. In some cases it is impossible to accurately translate from YAML to Puppet. In others, code that is generated from the conversion is syntactically correct but not idiomatic Puppet code.

### Named `eval` step

The `eval` step allows snippets of Puppet code to be expressed in YAML plans. When converting a multi-line `eval` step to Puppet code and storing the result in a variable, use the `with` lambda.

For example, here is a YAML plan with a multi-line `eval` step:

```
parameters:
  foo:
    type: Optional[Integer]
    description: foo
    default: 0

steps:
  - eval: |
      $x = $foo + 1
      $x * 2
    name: eval_step

return: $eval_step

```

And here is the same plan, converted to the Puppet language:

```
plan yaml_plans::with_lambda(
  Optional[Integer] $foo = 0
) {
  $eval_step = with() || {
    $x = $foo + 1
    $x * 2
  }

  return $eval_step
}

```

Writing this plan from scratch using the Puppet language, you would probably not use the lambda. In this example the converted Puppet code is correct, but not as natural or readable as it could be.

### Resource step variable interpolation

When applying Puppet resources in a `resource` step, variable interpolation behaves differently in YAML plans and Puppet language plans. To illustrate this difference, consider this YAML plan:

```
steps:
  - target: localhost
    description: Apply a file resource
    resources:
    - type: file
      title: '/tmp/foo'
      parameters:
        content: $facts['os']['family']
        ensure: present
  - name: file_contents
    description: Read contents of file managed with file resource
    eval: >
      file::read('/tmp/foo')
      
return: $file_contents

```

This plan performs `apply_prep` on a localhost target. Then it uses a Puppet `file` resource to write the OS family discovered from the Puppet `$facts` hash to a temporary file. Finally, it reads the value written to the file and returns it. Running `bolt plan convert` on this plan produces this Puppet code:

```
plan yaml_plans::interpolation_pp() {
  apply_prep('localhost')
  $interpolation = apply('localhost') {
    file { '/tmp/foo':
      content => $facts['os']['family'],
      ensure => 'present',
    }
  }
  $file_contents = file::read('/tmp/foo')

  return $file_contents
}

```

This Puppet language plan works as expected, whereas the YAML plan it was converted from fails. The failure stems from the `$facts`variable being resolved as a plan variable, instead of being evaluated as part of compiling the manifest code in an `apply`block.

### Dependency order

The resources in a `resources` list are applied in order. It is possible to set dependencies explicitly, but when doing so you must refer to them in a particular way. Consider the following YAML plan:

```
parameters:
  nodes:
    type: TargetSpec
steps:
  - name: pkg
    target: $nodes
    resources:
      - title: openssh-server
        type: package
        parameters:
          ensure: present
          before: File['/etc/ssh/sshd_config']
      - title: /etc/ssh/sshd_config
        type: file
        parameters:
          ensure: file
          mode: '0600'
          content: ''
          require: Package['openssh-server']

```

Executing this plan fails during catalog compilation because of how Bolt parses the resources referenced in the `before` and `require` parameters. You will see the error message `Could not find resource 'File['/etc/ssh/sshd_config']' in parameter 'before'`. The solution is to not quote the resource titles:

```
parameters:
  nodes:
    type: TargetSpec
steps:
  - name: pkg
    target: $nodes
    resources:
      - title: openssh-server
        type: package
        parameters:
          ensure: present
          before: File[/etc/ssh/sshd_config]
      - title: /etc/ssh/sshd_config
        type: file
        parameters:
          ensure: file
          mode: '0600'
          content: ''
          require: Package[openssh-server]

```

In general, declare resources in order. This is an unusual example to illustrate a case where parameter parsing leads to non-intuitive results.

