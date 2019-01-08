---
layout: default
title: "PE 2015.2 » Working With Environments"
subtitle: "Working With Environments"
canonical: "/pe/latest/console_classes_groups_environment_override.html"
---


We recommend the following environment-based workflow for testing new code in the node classifier, before pushing it to production. In this workflow, you create a set of node groups that are used exclusively for **assigning environments to nodes**, and another set of node groups that are used for **applying classes to nodes**. This workflow avoids the environment conflicts that can occur if a node is a member of two different node groups, each with a different environment specified.

> **Note:** To be assigned an environment, your nodes need to match an environment group. To be assigned classes, they need to match the applicable classification groups. 

<a href="./images/console/env_workflow.svg"><img src="./images/console/env_workflow.svg" alt="Environment Workflow" title="Click to enlarge"> (Click to enlarge)</a>

> **Important Note:** This is a design pattern for architecting and laying out node groups to implement the recommended workflow for using environments. The differences illustrated here between environment node groups and classification node groups are purely conventional. The Node Classifier makes no such distinction on a technical level, and does not show them separately. The Node Classifier is only aware of one kind of primitive: a node group. It is up to you to maintain design separation between environment node groups and classification node groups.

### Creating Environment Node Groups

1. Create a set of node groups that you will use exclusively for specifying which environment a node is in. Note that your PE install comes with a **Production environment** group already set up. The **Production environment** group has a rule that matches all nodes. All additional environment node groups that you create must have the **Production environment** as their parent (see the diagram above). This ensures that you are overriding the production environment that has been applied to all nodes by default through the **Production environment** group. 

2. In your *environment node groups*, add rules that match the nodes you want to include in the environment. **Do not add any classes to the environment node groups**.

3. When creating an environment node group, always select **This is an environment node group**. This ensures that the environment setting persists even when the matching nodes also match a classification node group that has a different environment specified. To select the **This is an environment node group** option:

    1. Click **Nodes** > **Classification**, and click the node group that you want to edit.

    2. Click **Edit node group metadata** at the upper right.

    3. Under **ENVIRONMENT**, select the **This is an environment node group** checkbox.

    4. To commit the change, click the commit button.

    In the **All node groups** view, an **ENV GROUP** tag is displayed next to the node groups that have **This is an environment node group** selected.

### Creating Classification Node Groups

1. Create a set of node groups that you will use exclusively for assigning classification to nodes. This is where you will add classes, along with rules that specify which nodes to match. You can leave the environment set to the default production environment, or change it to whatever environment you need to validate your classes and parameters against. **Do not** select **This is an environment node group** in any of your classification groups.

2. To set a test parameter, make a child group under a production node group. In the child group, set a parameter that overrides the value of the production parameter. To move the parameter to production, manually change the parameter in the production group and then delete the child group you were testing in.

3. To add parameters that are only available in the test environment, set the environment of the classification node group to `test` so that the node classifier validates your parameters against the test environment.

### Testing New Puppet Code with Canary Nodes and Alternate Puppet Environments

Puppet Enterprise allows you to centrally manage which nodes are in which environments. In most cases the environments are long-lived, such as dev, test, and prod, and nodes don't move between these environments once their initial environment has been set.

When an agent node matches the rules specified in the environment node groups [described above](#creating-environment-node-groups), the agent will end up with that environment regardless of any environments that have been specified in the agent's own puppet.conf file. We call this the *server-specified environment*.

The implication of this is that an individual agent *cannot* override the server-specified environment. Most of the time you don't want it to override the server-specified environment.

A notable exception is when you're changing Puppet code or data and you have a code promotion workflow based on puppet environments, like when using r10k and version control system feature branches. When you're writing new Puppet code, you can specify that certain nodes are allowed to use an *agent-specified environment* to quickly test the code in your feature branch, without permanently changing the environment that the node is in. 

Let's say you want to quickly test some new Puppet code. You don't want to permanently switch to using an agent-specified environment, but rather, you want to temporarily use a specific environment that maps to a feature branch in your version control system. In this case, you can use the `puppet agent -t --environment <YOUR TEST ENVIRONMENT>` command in combination with the workflow described below.   

#### Example use case:

* You want the node classifier to be the authoritative source of truth for the agent node's environment (server-specified environment).
* You want some of your nodes to get the production environment by default.
* You want some of your nodes to get the development environment by default.
* You want to allow a subset of your development nodes to override the server-specified environment so that, on-demand, they use a feature branch environment for Puppet runs to test new Puppet code.
* In this use case, you will not specify an environment locally in the node's puppet.conf file.

#### Workflow

PE comes with a production environment group already set up. All nodes are included in this environment group by default. Now let's say you create a "Development environment" group that is a child of production, and then a "Branch testing environment" group as a child of "Development environment." You will use members of "Branch testing environment" to test the new code in your feature branch. 

Steps:

1. Create the "Development environment" group and add nodes:
   - In the PE console UI, in **Nodes** > **Classification**, create a new node group called "Development environment" (for example). Set **Parent name** to **Production environment**, and set **Environment** to **development**. (**Note:** This assumes you've already created your development environment. For information on creating new environments, see [Creating Environments](/puppet/4.2/reference/environments_creating.html#creating-environments).)
   - Once you've created the environment group, click it to go to the node group details page. Click **Edit node group metadata** and select the **This is an environment group** checkbox.
   - In the **Rules** tab, add rules that match the nodes that you want to include in your development environment group, or pin the relevant nodes to the group.

2. Create the "Branch testing environment" group:
   - In the PE console UI, in **Nodes** > **Classification**, create a new node group called "Branch testing environment" (for example). Set **Parent name** to **Development environment**, and set **Environment** to **agent-specified**. 
   - Once you've created the environment group, click it to go to the node group details page. Click **Edit node group metadata** and select the **This is an environment group** checkbox.
 
3. Create a rule to match the nodes that you want to test on. In the **Rules** tab of the "Testing environment" group, add the rule `agent_specified_environment` `matches regex` `^.+`.


   This rule matches any nodes from the parent group that have the `agent_specified_environment` fact set to `true`. By matching nodes to this group, you are giving the nodes permission to override the server-specified environment, and use their locally-specified environment instead (the agent-specified environment). 
   
   Puppet runs will run with the Testing environment as specified by the server. By default, Puppet runs automatically every 30 minutes.
   
   A node gets the `agent_specified_environment` set to `true` in one of two ways: 
   
   1. If you run `puppet agent -t --environment <YOUR TEST ENVIRONMENT>`, then the `agent_specified_environment` fact is set to `<YOUR TEST ENVIRONMENT>` just for that Puppet run.
   
   2. If you specify an environment in the node's local `puppet.conf` file, then the `agent_specified_environment` fact is set to `<YOUR PUPPET.CONF ENVIRONMENT>` for as long as you continue to specify an environment locally.
   
4. Now, you can go ahead and test your new code on the agent node using an alternate environment. To do this, run `puppet agent -t --environment <YOUR TEST ENVIRONMENT>`. During the puppet run, the agent sets the `agent_specified_environment` fact to `<YOUR TEST ENVIRONMENT>`. Based on the rules set in step 3 above the server allows the agent to override its environment and the agent uses the test environment for a single Puppet run. 


>>**Note:** To apply an agent-specified environment for more than one run, specify the environment in the node's puppet.conf file. Doing this also sets the `agent_specified_environment` fact to `true`. The node will continue to get the agent-specified environment until you remove the environment from its `puppet.conf` file, or change the rules in the "Testing environment" group.


>>**Note:** If for some reason you want to ensure that the environment specified in the agent's puppet.conf file is applied longer term (for example if you always want the node to be a test node), you should include it in the Agent-Specified Environment node group by either pinning it to the group, or creating a group rule that matches the node. For information about using the Agent-Specified Environment node group, see [the documentation for that environment group](./console_classes_groups_preconfigured_groups.html#the-agent-specified-environment-node-group). 
* * *

- [Next: Using Event Inspector](./console_event-inspector.html)
