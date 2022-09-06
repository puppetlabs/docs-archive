# Exploring your catalog with the node graph

The node graph provides a graphic representation of any node's configuration, shows you the relationships between classes and resources, and lets you gain greater insight into just how the Puppet master compiles your code and ships it to an agent. Visualizing relationships between resources helps you organize them, creating more reliable deployments.

## How the node graph can help you

The node graph displays a node’s catalog \(as of the last Puppet run\) as an interactive visual map. The graph shows the desired state for each resource that PE manages, as well as each resource's status as of the last run, and helps you understand the dependencies between resources. It also allows you to visually identify complexity you may not need, and problematic dependencies that need your attention.

The node graph is an ideal tool for:

-   Gaining greater insight into your deployment.
-   Visualizing the relationship of resources on your nodes.
-   Diagnosing dependency loops and viewing all impacted resources.
-   Helping new users to understand relationships among classes and resources in their Puppet catalog.
-   Understanding defined resource types as they are deployed across your configuration.
-   Understanding the content of modules.

## Investigate a change with the node graph

Use the node graph to view the details of a change made to one of your nodes, and see how other resources and classes might be impacted.

### Access a node's graph

You can reach a node's graph from either the Overview page or the node's detail page. If you're using the node graph to find the cause of a particular run status, use the Overview page path. If you already know which node you're interested in, the Nodes page path is most efficient.

#### Procedure

1.  Locate a node you're interested in viewing:

    -   On the Overview page, filter by run status or fact value to locate the node.

    -   On the Nodes page, search for the node by name.

2.  Find and click the **Node graph** link:

    -   On the Overview page, the **Node graph** link is located to the right of the node's name.

    -   On the Nodespage, click the node name. The **View node graph** link is located at the top of the node details page.


### Focus on a change

When the node graph opens, you’ll see the containment view, which presents the catalog in its entirety. This can be a bit overwhelming, but you can use filters to quickly focus on the change event you're interested in.

#### Procedure

1.  In the **Filter:** bar, select **status**.

2.  Select a run status, such as **Corrective change**.

    The Node graph details pane at the right of the screen opens and displays a list of resources that match your filter criteria.

3.  Select any item on the list, and the graph repositions to focus on that resource.

    The Node graph details pane now shows additional data on current event status, as well as the object’s source file, code line number, relevant tags, and class containment information.


### Investigate dependencies

If your resource is part of a dependency chain, a **Dependency view** link is shown in the Node graph details pane. This link does not appear for resources without any dependencies.

#### Procedure

1.  Click **Dependency view**.

    The Node graph details pane displays a list of the resource’s ancestor and descendant dependencies.

2.  Hover over any item on the list, and the graph highlights that dependency path.

    **Note:** In the event that a dependency cycle is detected, the node graph issues a cycle warning message.

3.  Click **Exit dependency view** to return to the containment view.


