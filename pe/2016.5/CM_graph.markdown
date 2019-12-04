---
layout: default
title: "Explore your catalog with the node graph"
canonical: "/pe/latest/CM_graph.html"

---

PE's node graph shows you the relationships between classes and resources, and lets you gain greater insight into just how the Puppet master compiles your Puppet code and ships it to a Puppet agent.  

## How the node graph can help you

The node graph displays a node’s catalog (as of the last Puppet run) as an interactive visual map. The graph shows the desired state for each resource that PE manages, as well as each resource's status as of the last run, and helps you understand the dependencies between resources. It also allows you to visually identify complexity you may not need, and problematic dependencies that need your attention.

The node graph is an ideal tool for:
 
* Gaining greater insight into your PE deployment.
* Visualizing the relationship of resources on your nodes.
* Diagnosing dependency loops and viewing all impacted resources.
* Helping new users to understand relationships among classes and resources in their Puppet catalog.
* Understanding defined resource types as they are deployed across your configuration.
* Understanding the content of modules.

## Investigate a change with the node graph

Use the node graph to view the details of a change made to one of your nodes, and see how other resources and classes might be impacted. 

### Access a node's graph 

You can reach a node's graph from the Overview page in the PE console:

1. In the [PE console](./console_accessing.html), click **Configuration** > **Overview**. 
1. (Optional) Use the status counts section at the top of the page to filter nodes by [run status](./CM_overview.html#node-run-statuses). 
1. In the run status table below the status counts section, locate the name of the node you wish to view, and click **view node graph**. 

Alternately, access a node's graph from the node inventory section:

1. In the PE console, click **Nodes** > **Inventory**. 
1. In the **Node name** column, click the name of the node you wish to view. 
1. On the node detail page that opens, click **View node graph**.  

### Focus on a change

When the node graph opens, you’ll see the **containment view**, which presents the catalog in its entirety. This can be a bit overwhelming, but you can use filters to quickly focus on the change event you're interested in. 

1. In the **Filter:** bar, select **status** from the drop-down list.
1. Enter a run status, such as **Corrective change**, or select from the options available in the drop-down. The details panel at the right of the screen will open and display a list of resources that match your filter criteria. 
1. Select any item on the list, and the graph repositions to focus on that resource. The details pane now shows additional data on current event status, as well as the object’s source file, code line number, relevant tags, and class containment information.

### Investigate dependencies

If your resource is part of a dependency chain, the **Resource dependencies view** button is activated (this view remains inactive for resources without any dependencies).

1. Click **Resource dependencies view**. The details panel at the right of the screen will open and display a list of the resource's ancestor and descendant dependencies, as applicable. 
1. Hover over any item on the list, and the graph highlights that dependency path. 

> **Note:** In the event that a dependency cycle is detected, PE issues a cycle warning message. 


