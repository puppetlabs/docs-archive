---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Plan caveats and limitations

Some plan language and features are not available in `PE` or have some caveats.

## Bolt modules are not included

The following modules that come pre-installed with open source Bolt are not available by default in PE:

-   `aggregate`
-   `canary`
-   `puppetdb_fact`
-   `ruby_task_helper`
-   `python_task_helper`
-   `facts`
    -   The PE equivalent of `facts` is `factor_task`
-   `puppet_agent`
    -   The PE equivalent of `puppet_agent` is `pe_bootstrap`

## Plan language functions

The following Bolt plan functions will not work in PE and using them will cause a plan to fail:

-   `apply_prep`
-   `file::read`
-   `file::write`
-   `file::readable`
-   `file::exists`
-   `set_feature`
-   `add_to_group`
-   `set_config`

## Apply blocks

The `apply` feature for managing resources in a plan is not yet supported in `PE`. Using it will cause a plan to fail at the `apply` step.

## Target groups

Support for target groups is unavailable in PE. Using `add_to_group` will cause a plan to fail and referencing a group name in `get_targets` will not return any nodes. When using `get_targets` you **must** reference either node certnames or supply a PuppetDB query.

Here is an example of a plan using get targets with node certnames:

```
plan example::get_targets_example () {
  $nodes = get_targets([‘node1.mydomain.com’, ‘node2.mydomain.com’])
  run_command(‘whoami’, $nodes)
}
```

## Target features

Support for target features is unavailable in PE. Using `set_feature` will cause a plan to fail and referencing a target object’s features list will always return an empty array.

Here is an example of a plan that would return an empty features list:

```
plan example::empty_features_example (TargetSpec $node) {
  return get_target($node).features()
}
```

## Target configuration

While you can set up node transport configuration through the PE inventory for nodes to use SSH or WinRM, plans in PE do not support setting or changing the configuration settings for targets from within a plan. Using the `set_config` function in a plan will cause a plan to fail and referencing a target object’s configuration hash will always return an empty hash.

The use of URIs in a target name to override the transport is also not supported. All references to targets \(i.e. when using `get_targets`\) must be either PuppetDB queries or valid certnames that are already in the PE inventory.

Here is an example of a plan that uses `get_targets` correctly:

```
plan example::get_targets_example () {
  ## NOTE! If you used ssh://node1.mydomain.com as the first entry, this plan would fail!
  $nodes = get_targets([‘node1.mydomain.com’, ‘node2.mydomain.com’])
  run_command(‘whoami’, $nodes)
}
```

## `_run_as` options in plan functions

Plans in PE does not support the `_run_as` parameter for changing the user that accesses hosts or executes actions. If this parameter is supplied to any plan function, the plan will execute but the user will not change.

For example, the following plan will succeed, but will not run as `other_user`:

```
plan example::run_as_example (TargetSpec $nodes) {
  run_command(‘whoami’, $nodes, _run_as => ‘other_user’)
}
```

## The `localhost` target

The special target `localhost` is not available for plans in PE. Using `localhost` anywhere in a plan will result in a plan failure. If you need to run a plan on the PE master host, use the master's certname to reference it.

For example, you can use the following plan for PE master host `my-pe-master.company.com`:

```
plan example::referencing_the_master(){
  # Note that if you tried to use `localhost` instead of `my-pe-master` this plan would fail!
  run_command(‘whoami’, ‘my-pe-master.company.com’)
}
```

## Standard Puppet log functions

Puppet's built-in logging functions, like `notice` and `warn`, will not log anywhere. Plans with those functions will still work, but the logging messages will not appear.

If you need to use a logging function in your plan, the `out::message()` plan function will write an event to the orchestrator's event stream.

For example, the following will log the message correctly to the event stream:

```
plan example::logging(){
  out::message(“This will output to the orchestrator event stream”)
}
```

This will not log any messages:

```
plan example::bad_logging(){
  # neither of the following will output any messages anywhere
  notice(“this will go nowhere!”)
  warn(“this will also go nowhere!”)
}
```

## Script and file sources

When using `run_script` or `download_file`, the source location for the files **must** be from a module that uses a modulename/filename selector for a file or directory in `$MODULEROOT/files`. PE does not support file sources that reference absolute paths.

Here is an example of a module structure and a plan that correctly uses the modulename/filename selector:

```
example/
├── files
    └──my_script.sh
└── plans
    └──run_script_example.pp
```

```
plan example::run_script_example (TargetSpec $nodes) {
  run_script(‘example/my_script.sh’, $nodes)
}
```

## Code deployment for plans

For plans in PE to work, you must have code manager enabled to deploy code to your master.

Masters will now deploy a second codedir for plans to load code from. The second code location on masters have some effects on standard module functionality:

-   Installation modules using the `puppet module install` command will not work for plans because the puppet module tool will not install to the secondary location for plans. The `puppet module install` command will still work for normal Puppet code executed and compiled from Puppet Server.

-   A `$modulepath` configuration that uses fully qualified paths might not work for plans if they reference the standard `/etc/puppetlabs/code` location. We recommend using relative paths in `$modulepath`.


