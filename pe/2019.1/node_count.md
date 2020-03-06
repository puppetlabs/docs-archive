---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# How nodes are counted

Learn how PE determines you node count so you can stay within your licensing limit.

Your node count is the number of licensed nodes in your inventory. You are limited to a certain number of active nodes under your license before you hit your bursting limit. If you hit your bursting limit for four days in a month, you must purchase more nodes or remove some of your existing nodes.

Please note, "node" in this context includes agent nodes, agentless nodes, masters, compilers, nodes running in `noop`, and nodes that have been purge but still had prior activity within the same calendar month.

## Nodes that count

The following nodes are included in your limit:

-   Any node with a report in PuppetDB during the calendar month.
-   Any node that has executed a Puppet run, task, or plan in the orchestrator but does not have a report during the calendar month.

## Nodes that do not count

The following nodes do not count against your limit:

-   Nodes that are in the inventory service but are not used with Puppet runs, tasks, or plans.
-   Nodes that have been purged and have no reports or activity within the calendar month.

## Reaching the bursting limit

When you go above your node count limit, you enter what is called the bursting limit. The bursting limit lets you to exceed the number of nodes allowed under your license and enter a new threshold without extra charge. You are allowed four consecutive or non-consecutive days per calendar month to use your bursting limit. Any higher usage after the four days will require you to either purge or buy more nodes. The amount of time within your bursting limit does not matter for it to be counted as one day.

Here are some examples of how the bursting limit works, assuming you are licensed to use 1,000 nodes and your bursting limit is 2,000 nodes:

-   If you use 1,200 nodes for 2 hours one day, you will have three days left to use your bursting limit that month.
-   If you use 1,900 nodes for 23 hours one day, you will still have three days left that month.
-   If you use 1,500 nodes for 1 hour each day for four days, you will need to contact your Puppet representative to buy more nodes or purge some of your existing nodes until the next calendar month.

## When nodes are counted

PE tracks node counts daily from 12:00 midnight UTC to 12:00 midnight UTC.

The same time is used for the calendar month. For example, the month of September would include activity from 12:00 midnight UTC 01 September until 12:00 midnight UTC 30 September. After that point, the bursting limit restarts with a fresh four days in October.

## Viewing node counts

You can view your daily node counts in the console by navigating to the **License** page and scrolling to the **Calendar month usage** section. You can also view things like your subscription expiration date and any license warnings that appear in the console navigation. For example, if your license is expired or out of compliance.

On the command line, use the [Puppet orchestrator API: usage endpoint](orchestrator_api_usage_endpoint.md#) to see a list of daily node usage.

## Removing nodes

If you have unused nodes cluttering your inventory and are concerned about reaching your limit, read about [Adding and removing agent nodes](adding_and_removing_nodes.md#) and [Adding and removing agentless nodes](adding_and_removing_nodes_inventory.md#).

