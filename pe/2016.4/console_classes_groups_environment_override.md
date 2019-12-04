---
layout: default
title: "Working with environments"
canonical: "/pe/latest/console_classes_groups_environment_override.html"
---

## Setting up environment-based testing

An environment-based testing workflow is an effective approach for testing new code in the node classifier before pushing it to production. In this workflow, you create a set of node groups that are used exclusively for **assigning environments to nodes**, and another set of node groups that are used for **applying classes to nodes**. This workflow avoids the environment conflicts that can occur if a node is a member of two different node groups, each with a different environment specified.

> **Note:** To be assigned an environment, your nodes need to match an environment group. To be assigned classes, they need to match the applicable classification groups.

<a href="./images/console/env_workflow.svg"><img src="./images/console/env_workflow.svg" alt="Environment Workflow" title="Click to enlarge"> (Click to enlarge)</a>

> **Important Note:** This diagram shows a design pattern for architecting and laying out node groups to implement the recommended environment-based workflow. The differences illustrated here between environment node groups and classification node groups are purely conventional. The Node Classifier makes no such distinction on a technical level, and does not show them separately. The Node Classifier is only aware of one kind of primitive: a node group. It is up to you to maintain design separation between environment node groups and classification node groups.

Before testing and promoting data using an environment-based workflow, you must:

* [Create an environment]({{puppet}}/environments_creating.html) for testing
* [Create classification node groups](./console_classes_groups.html#creating-classification-node-groups)
* Add nodes to your classification groups [dynamically](./console_classes_groups.html#adding-nodes-dynamically) or [statically](./console_classes_groups.html#adding-nodes-statically)

### Testing and promoting a parameter

1. [Create a classification node group](./console_classes_groups.html#creating-classification-node-groups) with the test environment that is a child of a classification group that uses the production environment.

2. In the child group, set a test parameter. The test parameter overrides the value set by the parent group using the production environment.

3. If you're satisfied with your test results, manually change the parameter in the parent group.

4. (Optional) Delete the child test group.

### Testing and promoting a class

1. [Create a classification node group](./console_classes_groups.html#creating-classification-node-groups) with the test environment that is a child of a classification group that uses the production environment. The node classifier validates your parameters against the test environment.

2. If you're satisfied with your test results, change the environment for the node group from test to production.

### Testing new Puppet code with canary nodes and alternate Puppet environments

Puppet Enterprise allows you to centrally manage which nodes are in which environments. In most cases the environments are long-lived, such as dev, test, and prod, and nodes don't move between these environments after their initial environment has been set.

When an agent node matches the rules specified in the environment node groups, the agent will end up with that environment regardless of any environments that have been specified in the agent's own puppet.conf file. We call this the *server-specified environment*.

The implication of this is that an individual agent *cannot* override the server-specified environment. Most of the time you don't want it to override the server-specified environment.

A notable exception is when you're changing Puppet code or data and you have a code promotion workflow based on puppet environments, like when using r10k or Code Manager and version control system feature branches. When you're writing new Puppet code, you can specify that certain nodes are allowed to use an *agent-specified environment* to quickly test the code in your feature branch, without permanently changing the environment that the node is in.

Let's say you want to quickly test some new Puppet code. You don't want to permanently switch to using an agent-specified environment, but rather, you want to temporarily use a specific environment that maps to a feature branch in your version control system. In this case, you can use the `puppet agent -t --environment <YOUR TEST ENVIRONMENT>` command in combination with the workflow described below.

#### Example use case:

* You want the node classifier to be the authoritative source of truth for the agent node's environment (server-specified environment).
* You want some of your nodes to get the production environment by default.
* You want some of your nodes to get the development environment by default.
* You want to allow a subset of your development nodes to override the server-specified environment so that, on-demand, they use a feature branch environment for Puppet runs to test new Puppet code.
* In this use case, you will not specify an environment locally in the node's puppet.conf file.

#### Workflow

PE comes with a **Production environment** group that includes all nodes in the production environment by default. If you create a development environment group as a child of the production environment group, and a branch testing environment group as a child of the development environment group, you can then use members of the branch testing environment group to test new code.

In this example, we test the new code by adding to the branch testing environment group nodes that have `agent_specified_environment` set to `true`. We then complete a single puppet run on nodes within that branch that have an agent-specified environment.

Before you begin, you must [create an environment]({{puppet}}/environments_creating.html) for development.

1. [Create an environment node group](./console_classes_groups.html#creating-environment-node-groups) for development. Set the **Parent name** to **Production environment**, set the **Environment** to **development**, and select **Environment group**.
2. In the development environment group, add nodes [dynamically](./console_classes_groups.html#adding-nodes-dynamically) or [statically](./console_classes_groups.html#adding-nodes-statically).


3. [Create an environment node group](./console_classes_groups.html#creating-environment-node-groups) for branch testing. Set the **Parent name** to **Development environment**, set the **Environment** to **agent-specified**, and select **Environment group**.

4. Create a rule to match the nodes that you want to test on. In the **Rules** tab of the branch testing environment group, add the rule `agent_specified_environment` `matches regex` `^.+`.

   This rule matches any nodes from the parent group that have the `agent_specified_environment` fact set to `true`. By matching nodes to this group, you  give the nodes permission to override the server-specified environment, and use their agent-specified environment instead.

5. Run `puppet agent -t --environment <MYFEATUREBRANCH>`, where `<MYFEATUREBRANCH>` is the name of the puppet environment with your test code. If you're using the r10k or Code Manager workflow, this is the name of your Git branch. During the puppet run, the agent sets the `agent_specified_environment` fact to `<MYFEATUREBRANCH>`. Based on the rules you set in step 4, the server allows the agent to override its environment and the agent uses the test environment for a single Puppet run.


>**Note:** To apply an agent-specified environment for more than one run, specify the environment in the node's puppet.conf file. Doing this also sets the `agent_specified_environment` fact to `true`. The node will continue to get the agent-specified environment until you remove the environment from its `puppet.conf` file, or change the rules in the "Testing environment" group.
>
>If you want to specify a node's environment long-term -- for example, if you always want the node to be a test node -- include it in the agent-specified environment node group. For information about using the agent-specified environment node group, see [the documentation for that environment group](./console_classes_groups_preconfigured_groups.html#the-agent-specified-environment-node-group).

