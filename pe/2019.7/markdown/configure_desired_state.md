# Configure your desired state

Tell PE how your infrastructure should be configured by grouping and classifying nodes based on their function. Before you begin, think of which of your inventory nodes you want to configure with Apacheservices.

## Create your classification group

Create a classification node group. This is a parent group to other node groups you create that contain classification data. You only need to set this up one time.

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group:

    -   **Parent name**: `All Nodes`
    -   **Group name**: `All Classification`
    -   **Environment**: `production`
    -   **Environment group**: do not select
3.  Click **Add**.


You have added a classification group. Next, create a node group and add it as a child of this classification group.

## Create your `apache` node group

After you create your parent classification group, make the `apache` node group a child of it.

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group:

    -   **Parent name**: `All Classification`
    -   **Group name**: `apache`
    -   **Environment**: `production`
    -   **Environment group**: do not select
3.  Click **Add**.


You now have a node group for nodes you want to configure Apache on. Next, determine which nodes from your inventory you want to run Apache on and add them to the `apache` node group.

## Add nodes to the `apache` node group

Once you determine which nodes from your inventory should be in your `apache` node group, pin the nodes to the group. Pinning allows you to add nodes to a group one at a time. You can also dynamically add nodes to a node group based on rules you set in the console, which is helpful for large inventories.

If you are stuck on deciding which nodes should be in your `apache` node group, read about [Best practices for classifying node groups](grouping_and_classifying_nodes.md#).

1.  In the console, click **Classification** and select the `apache` node group you just created.

2.  On the **Rules** tab, under **Certname**, enter the certname of a node you want to add to the `apache` node group.

3.  Click **Pin node**.

4.  Repeat for additional nodes you want to add to the `apache` group.

5.  When you are done pinning nodes, commit changes.


You have added your `apache` nodes into a node group. For more information on adding and classifying nodes, read about [Making changes to node groups](making_changes_to_node_groups.md#) and [Grouping and classifying nodes](grouping_and_classifying_nodes.md#).

## Run Puppet on your Apache nodes

Run Puppet in the console to enforce your desired state on the `apache` node group you created.

1.  In the console, on the **Jobs** page, click **Run Puppet**. 

2.  Under **Run options**, do not select anything.

3.  From the list of target types, select **Node group**.

4.  In the **Chose a node group box**, search for the `apache` node group and click **Select**.

5.  Click **Run job**.

    View the job status and a list of previous Puppet jobs on the **Jobs** page.


Congratulations! You have run Puppet on your `apache` node group. In the future, the apache nodes will check in with PE every 30 minutes to confirm that their apache configuration matches what you've specified, and PE will correct it if it doesn't.

Next, learn how to use roles and profiles to efficiently manage system configurations.

