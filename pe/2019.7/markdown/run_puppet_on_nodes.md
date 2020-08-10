# Running Puppet on nodes

Puppet automatically attempts to run on each of your nodes every 30 minutes. To trigger a Puppet run outside of the default 30-minute interval, you can manually run Puppet.

In a Puppet run, the master and agent nodes perform the following actions:

1.  The agent node sends facts to the master and requests a catalog.

2.  The master compiles and returns the agent’s catalog.

3.  The agent applies the catalog by checking each resource the catalog describes. If it finds any resources that are not in the desired state, it makes the necessary changes.


**Note:** Puppet run behavior differs slightly if static catalogs are enabled.

**Related information**  


[Static catalogs in Puppet Enterprise](static_catalogs.md#)

## Running Puppet with the orchestrator

The Puppet orchestrator is a set of interactive tools used to deploy configuration changes when and how you want them. You can use the orchestrator to run Puppet from the console, command line, or API.

You can use the orchestrator to enforce change based on a:

-   selection of nodes – from the console or the command line:

```no-highlight
puppet job run --nodes <COMMA-SEPARATED LIST OF NODE NAMES>
```

-   PQL nodes query – from the console or the command line, for example:

```
puppet job run --query 'nodes[certname] { facts {name = "operatingsystem" and value = "Debian" }}'
```

-   application or application instance - from the command line.


If you're putting together your own tools for running Puppet or want to enable CI workflows across your infrastructure, use the orchestrator API.

**Related information**  


[Running Puppet on demand from the console](running_puppet_on_demand_in_the_console.md#)

## Running Puppet with SSH

Run Puppet with SSH from an agent node.

SSH into the node and run `puppet agent --test` or `puppet agent -t`.

## Running Puppet from the console

In the console, you can run Puppet from the node detail page for nodes that have an agent connection.

Run options include:

-   `No-op` – Simulates changes without actually enforcing a new catalog. Nodes with `noop = true` in their `puppet.conf` files always run in no-op mode.

-   `Debug` – Prints all messages available for use in debugging.

-   `Trace` – Prints stack traces on some errors.

-   `Evaltrace` – Shows a breakdown of the time taken for each step in the run.


When the run completes, the console displays the node’s run status.

**Note:** The **Run Puppet** button is not available if an agent does not have an active websocket session with the PCP broker, or if the node's connection method is SSH, WinRM, or a network device transport.

**Related information**  


[Node run statuses](monitor_infrastructure_state.md#)

## Activity logging on console Puppet runs

When you initiate a Puppet run from the console, the Activity service logs the activity.

You can view activity on a single node by selecting the node, then clicking the **Activity** tab.

Alternatively, you can use the Activity Service API to retrieve activity information.

**Related information**  


[Activity service API](activity_api.md)

## Troubleshooting Puppet run failures

Puppet creates a **View Report** link for most failed runs, which you can use to access events and logs.

This table shows some errors that can occur when you attempt to run Puppet, and suggestions for troubleshooting.

|**Error**|**Possible Cause**|
|Changes could not be applied|Conflicting classes are a common cause. Check the log to get more detail.|
|Noop, changes could not be applied|Conflicting classes are a common cause. Check the log to get more detail.|
|Run already in progress|Occurs when a run is triggered in the command line or by another user, and you click **Run**.|
|Run request times out|Occurs if you click **Run** and the agent isn’t available.|
|Report request times out|Occurs when the report is not successfully stored in PuppetDB after the run completes.|
|Invalid response \(such as a 500 error\)|Your Puppet code might be have incorrect formatting.|
|Button is disabled and a run is not allowed.|The user has permission, but the agent is not responding.|

