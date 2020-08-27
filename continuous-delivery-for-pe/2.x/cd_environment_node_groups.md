---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Create environment node groups

In order for code deployments managed by Continuous Delivery for PE to work correctly, your environment node groups should be set up in a specific hierarchy.

## About this task

Continuous Delivery for PE deploys changes to environment node groups. By setting up environment node groups, you define the groups of nodes that you can choose to deploy changes to.

## Procedure

1.  In the PE console, click **Classification**.

2.  Click **Add group...** and create a new node group with the following specifications:

    -   Parent name: All Nodes

    -   Group name: All Environments

    -   Environment: production

    -   Environment group: yes

    -   Description: Environment group parent and default

3.  Open the new All Environments node group and add a new rule:

    -   Fact: name

    -   Operator: ~

    -   Value: .\*

4.  Edit the Agent-specified environment node group so that All Environments is its parent. This group should have no rules, and won't match any nodes.

5.  Edit the Production environment node group so that All Environments is its parent. If necessary, modify its rules so that it matches only the correct nodes.

6.  For each of your environment groups \(such as testing, staging, and production\), create an environment node group.

    1.  Create a Git branch to represent the environment.

    2.  Run `puppet code deploy <ENVIRONMENT_NAME>`.

    3.  Create a new environment node group with the following specifications:

        -   Parent name: All Environments

        -   Group name: All <ENVIRONMENT\_NAME\>

        -   Environment: <ENVIRONMENT\>

        -   Environment group: Yes

    4.  Associate the relevant nodes with the environment group by creating rules or pinning nodes.

        Best practices for associating nodes with environment node groups:

        -   Use the `pp_environment` trusted fact, or a similar custom fact, to define which environment each node belongs to. Write a rule in each environment group that uses `pp_environment` or your custom fact to match nodes.

        -   See if other facts or trusted facts can be used to create rules that match nodes to one and only one environment group

        -   If trusted facts, custom facts, or other facts cannot be used to determine node environments, use pinning. Pin each node to only one environment group.

    5.  Specify the Git branch corresponding to the environment.

7.  For each of your newly created environment groups, create a child environment group. Nodes from the parent environment group are allowed to drop into this exception group to test code from Git feature branches. Give each child environment group the following specifications:

    -   Parent name: All <ENVIRONMENT\>

    -   Group name: <ENVIRONMENT\> one-time run exception

    -   Environment: Agent-specified

    -   Environment group: Yes

    -   Description: Allow <ENVIRONMENT\> nodes to request a different Puppet environment for a one-time run

    Once the child environment node group is set up, give it the rule `(agent_specified_environment ~ .+)`. Do not pin any nodes to this node group.  


## Result:

The resulting environment node groups has a format similar to this:

![](env-node-groups.png)

Now that your environment nodes groups are configured, we can deploy new code to your nodes.

