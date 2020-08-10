---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Inspecting plans

View the plans you have installed and which ones you have permissions to run, as well as details about individual plans.

Log into your master or client tools workstation and run one of the following commands to see information about your plan inventory:

-   `puppet plan show` - View a list of your permitted plans.

-   `puppet plan show --all` - View a list of all installed plans.

Use the command `puppet plan show <PLAN NAME>` to view plan metadata. The output includes the command format and parameters available to use for an individual plan.

For example, this plan allows a `$nodes` parameter and a `$version` parameter, specified as data types `TargetSpec` and `Integer`.

```
plan infra::upgrade_apache (
  TargetSpec $nodes,
  Integer $version,
){
run_task(‘package’, $nodes, name => ‘apache’, action => ‘upgrade’, version
 => $version)
}
```

To view plan metadata in the console, choose which plan you want to run in the **Plan** field and click on the **View plan metadata** link.

