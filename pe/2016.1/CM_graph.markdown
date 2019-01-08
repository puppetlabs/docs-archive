---
layout: default
title: "Explore your catalog with the node graph"
canonical: "/pe/latest/CM_graph.html"

---

PE's node graph shows you the relationships between classes and resources, and lets you gain greater insight into just how the Puppet master compiles your Puppet code and ships it to a Puppet agent.  

The node graph displays a node’s catalog (as of the last Puppet run) as an interactive visual map. The graph shows the desired state for each resource that PE manages, as well as each resource's status as of the last run, and helps you understand the dependencies between resources. It also allows you to visually identify complexity you may not need, and problematic dependencies that need your attention. 

## View the node graph 

To access a node’s graph, navigate to the **Overview** page of the [PE console’s](./console_accessing.html) Configuration section, and then click the **View graph** link at the right of your selected node’s listing. 

![node1][node1]

[node1]: ./images/console/burnside_cm_node1.png

## Visualize resource and class relationships

When you click the **View graph** link, the node’s graph opens. Note the two buttons at the top of the graph: **Containment view** and **Resource dependencies view**.

![node2][node2]

[node2]: ./images/console/burnside_cm_node2.png

You’re currently looking at the containment view, which presents you with the catalog in its entirety. This can be a bit overwhelming, but you can use the **Highlight** box to quickly filter this view to see only the classes and/or resources you're interested in. Apply filters to search for specific objects or filter by event status or tag. You can also explore the graph by simply scrolling, zooming, and clicking on objects. 

Click on a specific resource. The graph repositions to zoom in on the selected item and the detail pane opens. The detail pane contains additional data on current event status, as well as the object’s source file, code line number, relevant tags, and class containment information.
 
![node3][node3]

[node3]: ./images/console/burnside_cm_node3.png

## Investigate dependencies

If your resource is part of a dependency chain, the **Resource dependencies view** button is activated (this view remains inactive for resources without any dependencies).

![node4][node4]

[node4]: ./images/console/burnside_cm_node4.png

Click this button to view the dependency relationships between the selected resource and other resources. The detail pane in this view provides further details on ancestor and dependant relationships between linked resources. 

In the event that a dependency cycle is detected, PE issues a cycle warning message. 

![node5][node5]

[node5]: ./images/console/burnside_cm_node5.png

## How the node graph can help you

The node graph is an ideal tool for:
 
* Gaining greater insight into your PE deployment.
* Visualizing the relationship of resources on your nodes.
* Diagnosing dependency loops and viewing all impacted resources.
* Helping new users to understand relationships among classes and resources in their Puppet catalog.
* Understanding defined resource types as they are deployed across your configuration.
* Understanding the content of modules.
