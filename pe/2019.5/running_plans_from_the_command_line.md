---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Running plans from the command line

Run a plan using the `puppet plan run` command.

## About this task

## Procedure

1.  On the command line, run the command `puppet plan run` with the following information included:

    -   The full name of the plan, formatted as `<MODULE>::<PLAN>`.
    -   Any plan parameters.
    -   Credentials, if required, formatted with the `--user` and `--password` flags.
    For example, if a plan defined in `mymodule/plans/myplan.pp` accepts a `load_balancer` parameter, run:

    ```
    puppet plan run mymodule::myplan load_balancer=lb.myorg.com
    
    ```

    You can pass a comma-separated list of node names, wildcard patterns, or group names to a plan parameter that is passed to a run function or that the plan resolves using `get_targets`.


## Plan command options

The following are common options you can use with the `plan` action. For a complete list of global options run `puppet plan --help`.

-   `--params` - A string value used to specify a JSON object that includes the parameters, or specify the path to a JSON file containing the parameters, prefaced with `@`. For example, `@/path/to/file.json`. Do not use this flag if specifying parameter-value pairs inline.
-   `--environment` or `-e` - The name of the environment where plans are installed.
-   `--description` - A flag used to provide a description for the job to be shown on the job list and job details pages and returned with the `puppet job show` command. It defaults to empty.

You can pass parameters into the plan one of two ways:

-   Inline, using the `<PARAMETER>=<VALUE>` syntax

-   With the `--params` option, as a JSON object or reference to a JSON file.


For example, review this plan:

```
plan example::test_params(Targetspec $nodes, String $command){
  run_command($command, $nodes)
}
```

You can pass parameters using either option below:

-   ```
puppet plan run example::test_params nodes=my-node.company.com command=whoami
```

-   ```
puppet plan run example::test_params --params ‘{“nodes”:”my-node.company.com”, “command”:”whoami”}’
```


You can't combine these two ways of passing in parameters. Choose either inline or `--params`.

If you use the inline way, parameter types other than string, integer, double, and Boolean will be interpreted as strings. Use the `--params` method if you want them read as their original type.

