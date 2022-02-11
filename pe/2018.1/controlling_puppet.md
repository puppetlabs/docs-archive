# Controlling Puppet

PE's configuration management features rely on the Puppet agent service, which runs on every node and fetches configurations from the Puppet master server.

By default, the agent idles in the background and performs a run every 30 minutes, but MCollective can give complete control over this behavior.

**Note:** The MCollective engine cannot trigger a node's *very first* agent run. A node's first run will happen automatically within 30 minutes after you sign its certificate.

**Related information**  


[Logging into MCollective](invoking_mcollective_actions.md#)

## Running Puppet on demand

Use the `runonce` action to trigger an immediate Puppet run on a few nodes. If you need to run Puppet on many nodes \(more than ten\), you should see the "many nodes" section below.

### Behavior differences: Running vs. stopped

You can trigger on-demand runs whether the `puppet` service is running or stopped, but on \*nix nodes these cases will behave slightly differently:

-   When the service is **running**, all of the selected nodes will begin a run *immediately,* and you *cannot* specify any special options like `noop` or `tags`; they will be ignored. This behavior is usually fine but sometimes undesirable.

-   When the service is **stopped**, the selected nodes will randomly stagger the start of their runs \("splay"\) over a default interval of *two minutes.* If you wish, you *can* specify special options, including a longer interval \("splaylimit"\). You can also set the `force` option to `true` if you want the selected nodes to start immediately. This behavior is more flexible and resilient.


This difference only affects \*nix nodes; Windows nodes always behave like a **stopped** \*nix node.

### Triggering on-demand runs on the command line

While logged in to the Puppet master server as `peadmin`, run the `mco puppet runonce` command.

```
$ mco puppet runonce -I web01.example.com -I web02.example.com
$ mco puppet runonce -F kernelversion=2.6.32
```

Be sure to specify a filter to limit the number of nodes; you should generally invoke this action on fewer than 10 nodes at a time, especially if the agent service is running and you cannot specify extra options \(see above\).

**Additional command options**

If the agent service is stopped \(on affected \*nix nodes; see above\), you can change the way Puppet runs with command line options. You can see a list of these by running `mco puppet --help`.

```
--force                      Bypass splay options when running
--server SERVER              Connect to a specific server or port
--tags, --tag TAG            Restrict the run to specific tags
--noop                       Do a no-op run
--no-noop                    Do a run with no-op disabled
--environment ENVIRONMENT    Place the node in a specific environment for this run
--splay                      Splay the run by up to splaylimit seconds
--no-splay                   Do a run with splay disabled
--splaylimit SECONDS         Maximum splay time for this run if splay is set
--ignoreschedules            Disable schedule processing
```

## Running Puppet on many nodes in a controlled series

If you want to trigger a run on a large number of nodes—more than ten—the `runonce` action isn't always the best tool. You can splay or batch the runs, but this requires you to guess how long each run is going to take, and a wrong guess can either waste time or temporarily overwhelm the Puppet master server.

Instead, use the special `runall` action of the `mco puppet` subcommand.

```
$ mco puppet runall 5 -F operatingsystem=CentOS -F operatingsystemrelease=6.4
```

This action requires an argument, which must be the number of nodes allowed to run at once. It invokes a run on that many nodes, then only starts the next node when one has finished. This prevents your Puppet master from being overwhelmed by the herd and will delay only as long as is necessary. The ideal concurrency will vary from site to site, depending on how powerful your Puppet master server is and how complex your configurations are.

The `runall` action can take extra options like `--noop` as described for the `runonce` action; however, note that restrictions still apply for \*nix nodes where the pe-puppet service is running.

## Enabling and disabling Puppet agent

Disabling Puppet will block all runs, including both scheduled and on-demand runs. This is usually used while you investigate some kind of problem. Use the `enable` and `disable` actions of the `puppet` plugin.

The `disable` action accepts an optional reason for the lockdown; take advantage of this to keep your colleagues informed. The reason will be shown when checking Puppet's status on those nodes.

After a node has been disabled for an hour, it will appear as "unresponsive" in the console's node views, and will stay that way until it is re-enabled.

### Enabling and disabling Puppet agent on the command line

While logged in to the Puppet master server as `peadmin`, run `mco puppet disable` or `mco puppet enable` with or without a filter.

**Example:** You noticed runs failing on a load balancer and expect they'll start failing on the other ones too:

```
$ mco puppet disable "Investigating a problem with the haproxy module. -NF" -C /haproxy/
```

## Starting and stopping the Puppet agent service

You can start or stop the `puppet` service with the `start` and `stop` actions of the `service` plugin. This can be useful if you need to do no-op runs, or if you wish to stop all scheduled runs and only run agents on demand.

### Starting and stopping the agent service on the command line

While logged in to the Puppet master server as `peadmin`, run `mco service puppet stop` or `mco service puppet start` with or without a filter.

**Example**: To prepare all web servers for a manifest update and no-op, run:

```
$ mco service puppet stop -C /apache/
```

## Viewing the Puppet agent's status

The agent can be in various states. The MCollective engine lets you check the current status on any number of nodes.

### Viewing the agent's state on the command line

While logged in to the Puppet master server as `peadmin`, run `mco puppet status` with or without a filter. This returns an abbreviated status for each node and a summarized breakdown of how many nodes are in which conditions.

```
$ mco puppet status
```

### Viewing disable messages

The one thing `mco puppet status` doesn't show is the reason why the agent was disabled. If you're checking up on disabled nodes, you can get a more raw view of the status by running `mco rpc puppet status` instead. This will display the reason in the **Lock Message** field.

**Example:** Get the detailed status for every disabled node, using the `puppet` data plugin:

```
$ mco rpc puppet status -S "puppet().enabled=false"
```

## Viewing statistics about recent runs

Puppet keeps records of the last run, including the amount of time spent per resource type, the number of changes, number of simulated changes, and the time since last run. You can retrieve and summarize these statistics with the MCollective engine.

### Viewing statistics on the command line

You can view population summary graphs or detailed statistics.

-   Population summary graphs

    You can get sparkline graphs for the last run statistics across all your nodes with the `mco puppet summary` command. This shows the distribution of your nodes, so you can see whether a significant group is taking notably longer or seeing more changes.

    ```
    $ mco puppet summary
    Summary statistics for 10 nodes:
    
                      Total resources: ▇▁▁▂▁▁▁▂▁▁▁▁▁▁▁▃▁▁▁▁  min: 93.0   max: 155.0
                Out Of Sync resources: ▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 0.0    max: 0.0
                     Failed resources: ▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 0.0    max: 0.0
                    Changed resources: ▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 0.0    max: 0.0
      Config Retrieval time (seconds): ▇▇▃▃▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 1.9    max: 5.8
             Total run-time (seconds): ▇▆▃▄▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 2.2    max: 6.7
        Time since last run (seconds): ▇▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▂  min: 314.0  max: 23.4k
    ```

-   Detailed statistics

    While logged in to the Puppet master server as `peadmin`, run `mco rpc puppet last_run_summary` with or without a filter. This returns detailed run statistics for each node. \(Note that this uses the `rpc` subcommand instead of the `puppet` subcommand.\)


