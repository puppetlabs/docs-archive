---
layout: default
title: "Controlling Puppet"
canonical: "/pe/latest/orchestration_puppet.html"
---

[certsign]: ./install_agents.html#managing-agent-certificate-requests
[peadmin]: ./mco_invoke_cli.html#logging-in-as-peadmin
[inpage_stop]: #start-and-stop-the-puppet-agent-service
[filter]: ./mco_invoke_cli.html#filtering-actions
[environment]: /guides/environment.html
[tags]: {{puppet}}/lang_tags.html
[batch]: ./mco_invoke_cli.html#batching
[data_plugins]: ./mco_invoke_cli.html#data-plugins


Puppet Enterprise (PE)'s configuration management features rely on the **Puppet agent service**, which runs on every node and fetches configurations from the Puppet master server.

By default, Puppet agent idles in the background and performs a run every 30 minutes, but MCollective can give complete control over this behavior. See the table of contents above for an overview of the available features.

> **Note:** The MCollective engine cannot trigger a node's _very first_ Puppet agent run. A node's first run will happen automatically within 30 minutes after you [sign its certificate][certsign].

Basics
-----

### Invoking actions

You can use the MCollective engine to control Puppet from the Puppet master server's Linux **command line**.

> This page provides basic instructions on invoking actions, including how to log in: [Invoking actions on the command line][cli]

On the command line, most of these tasks use the `mco puppet` subcommand.

[cli]: ./mco_invoke_cli.html

### The Puppet agent service

In PE, Puppet agent runs in the background as a system service.

* On \*nix nodes, this service is named `puppet`.
* On Windows nodes, this service's display name is `Puppet Agent` and its short name is `puppet`.

### Agent status: Enabled, disabled, etc.

Puppet agent can be in many possible states, which are represented by three attributes:

* **Running** or **stopped** --- whether the agent _service_ (`puppet`) is running in the background. Even if it's running, the service may or may not be doing anything at the moment. If the service is stopped, no scheduled runs will occur but you can still trigger on-demand runs.
* **Applying,** **idling,** or **neither** --- whether Puppet agent is in the process of applying a configuration. Idling is only applicable if the service is running, but Puppet may be applying an on-demand configuration even if the service is stopped.
* **Enabled** or **disabled** --- whether there's a lockfile preventing Puppet agent from performing _any_ configuration runs. If Puppet agent is disabled, the service can idle in the background but no configurations can be applied --- even on-demand runs will be rejected until the agent is re-enabled.

The MCollective engine can trigger on-demand Puppet runs unless the agent is **applying** or **disabled**. Scheduled runs will only take place if the agent is both **running** and **enabled.**

[↑ Back to top](#content)

* * *

Run Puppet on demand
-----

Use the `runonce` action to trigger an immediate Puppet run on a few nodes. If you need to run Puppet on many nodes (more than ten), you should see [the "many nodes" section below](#run-puppet-on-many-nodes-in-a-controlled-series).

[options]: #behavior-differences-running-vs-stopped

> ### Behavior differences: Running vs. stopped
>
> You can trigger on-demand Puppet runs whether the `puppet` service is running or stopped, but on \*nix nodes these cases will behave slightly differently:
>
> * When the service is **running**, all of the selected nodes will begin a run _immediately,_ and you _cannot_ specify any special options like `noop` or `tags`; they will be ignored. This behavior is usually fine but sometimes undesirable.
> * When the service is **stopped**, the selected nodes will randomly stagger the start of their runs ("splay") over a default interval of _two minutes._ If you wish, you _can_ specify special options, including a longer interval ("splaylimit"). You can also set the `force` option to `true` if you want the selected nodes to start immediately. This behavior is more flexible and resilient.
>
> This difference only affects \*nix nodes; Windows nodes always behave like a **stopped** \*nix node. The difference will be addressed in a future version of PE; for now, you may wish to [stop the `pe-puppet` service][inpage_stop] before trying to do `noop` or `tags` runs.

### On the command line

While [logged in to the Puppet master server as `peadmin`][peadmin], run the `mco puppet runonce` command.

    $ mco puppet runonce -I web01.example.com -I web02.example.com
    $ mco puppet runonce -F kernelversion=2.6.32

Be sure to [specify a filter][filter] to limit the number of nodes; you should generally invoke this action on fewer than 10 nodes at a time, especially if the agent service is running and you cannot specify extra options ([see above][options]).

#### Extra options

If the agent service is stopped (on affected \*nix nodes; [see above][options]), you can change the way Puppet runs with command line options. You can see a list of these by running `mco puppet --help`.

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

The most useful options are:

* `--noop`, which causes Puppet agent to only _simulate_ changes, and submit a report describing what _would_ have changed in a real run. Useful for safely testing new manifests. If you have configured Puppet agent to _always_ run in no-op mode (via `/etc/puppetlabs/puppet/puppet.conf`), you can use `--no-noop` to do an enforcing Puppet run.
* `--environment ENVIRONMENT`, which causes Puppet agent to run in the specified [environment][]. Also useful for testing new manifests on a subset of nodes.
* `--tags TAGS`, which takes a comma-separated list of [tags][] and applies only resources with those tags. Tags can be class names, and this is a fast way to test changes to a single class without performing an entire Puppet run.
* `--server SERVER`, which causes Puppet agent to contact a different Puppet master than normal. Also useful for testing new manifests (or a new version of PE) on a subset of nodes.

[↑ Back to top](#content)

* * *

Run Puppet on many nodes in a controlled series
-----

> **Note:** In PE, this feature is only available on the command line; you cannot do a controlled run series in the console.

If you want to trigger a run on a large number of nodes --- more than ten --- the `runonce` action isn't always the best tool. You can splay or [batch][] the runs, but this requires you to guess how long each run is going to take, and a wrong guess can either waste time or temporarily overwhelm the Puppet master server.

Instead, use the special `runall` action of the `mco puppet` subcommand.

    $ mco puppet runall 5 -F operatingsystem=CentOS -F operatingsystemrelease=6.4

This action requires an argument, which must be the number of nodes allowed to run at once. It invokes a run on that many nodes, then only starts the next node when one has finished. This prevents your Puppet master from being overwhelmed by the herd and will delay only as long as is necessary. The ideal concurrency will vary from site to site, depending on how powerful your Puppet master server is and how complex your configurations are.

The `runall` action can take extra options like `--noop` [as described for the `runonce` action](#extra-options); however, note that [restrictions still apply for \*nix nodes where the pe-puppet service is running][options].

[↑ Back to top](#content)

* * *

Enable and disable Puppet agent
-----

Disabling Puppet will block **all** Puppet runs, including both scheduled and on-demand runs. This is usually used while you investigate some kind of problem. Use the `enable` and `disable` actions of the `puppet` plugin.

The `disable` action accepts an optional reason for the lockdown; take advantage of this to keep your colleagues informed. The reason will be shown when checking Puppet's status on those nodes.

After a node has been disabled for an hour, it will appear as "unresponsive" in the console's node views, and will stay that way until it is re-enabled.

### On the command line

While [logged in to the Puppet master server as `peadmin`][peadmin], run `mco puppet disable` or `mco puppet enable` with or without a filter.

**Example:** You noticed Puppet runs failing on a load balancer and expect they'll start failing on the other ones too:

    $ mco puppet disable "Investigating a problem with the haproxy module. -NF" -C /haproxy/

[↑ Back to top](#content)

* * *

Start and stop the Puppet agent service
-----

You can start or stop the `puppet` service with the `start` and `stop` actions of the `service` plugin. This can be useful if you need to do no-op runs, or if you wish to stop all scheduled runs and only run Puppet agent on demand.

### On the command line

While [logged in to the Puppet master server as `peadmin`][peadmin], run `mco service puppet stop` or `mco service puppet start` with or without a filter.

**Example**: To prepare all web servers for a manifest update and no-op, run:

    $ mco service puppet stop -C /apache/

[↑ Back to top](#content)

* * *

View Puppet agent's status
-----

[As mentioned above](#agent-status-enabled-disabled-etc), Puppet agent can be in various states. The MCollective engine lets you check the current status on any number of nodes.

### On the command line

#### Aggregate status

While [logged in to the Puppet master server as `peadmin`][peadmin], run `mco puppet status` with or without a filter. This returns an abbreviated status for each node and a summarized breakdown of how many nodes are in which conditions.

    $ mco puppet status

#### Viewing disable messages

The one thing `mco puppet status` doesn't show is the [reason why Puppet agent was disabled](#enable-and-disable-puppet-agent). If you're checking up on disabled nodes, you can get a more raw view of the status by running `mco rpc puppet status` instead. This will display the reason in the __Lock Message__ field.

**Example:** Get the detailed status for every disabled node, using the `puppet` [data plugin][data_plugins]:

    $ mco rpc puppet status -S "puppet().enabled=false"

[↑ Back to top](#content)

* * *

View statistics about recent runs
-----

Puppet keeps records of the last run, including the amount of time spent per resource type, the number of changes, number of simulated changes, time since last run, etc. You can retrieve and summarize these statistics with the MCollective engine.

### On the command line

#### Population summary graphs

You can get sparkline graphs for the last run statistics across all your nodes with the `mco puppet summary` command. This shows the distribution of your nodes, so you can see whether a significant group is taking notably longer or seeing more changes.

    $ mco puppet summary
    Summary statistics for 10 nodes:

                      Total resources: ▇▁▁▂▁▁▁▂▁▁▁▁▁▁▁▃▁▁▁▁  min: 93.0   max: 155.0
                Out Of Sync resources: ▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 0.0    max: 0.0
                     Failed resources: ▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 0.0    max: 0.0
                    Changed resources: ▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 0.0    max: 0.0
      Config Retrieval time (seconds): ▇▇▃▃▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 1.9    max: 5.8
             Total run-time (seconds): ▇▆▃▄▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁  min: 2.2    max: 6.7
        Time since last run (seconds): ▇▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▂  min: 314.0  max: 23.4k

#### Detailed statistics

While [logged in to the Puppet master server as `peadmin`][peadmin], run `mco rpc puppet last_run_summary` with or without a filter. This returns detailed run statistics for each node. (Note that this uses the `rpc` subcommand instead of the `puppet` subcommand.)


