---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Introducing deployments

Deployments in Continuous Delivery for PE use the PE tools you're familiar with--Code Manager, orchestration, and Puppet environments--to deploy your new code, but don't require you to use Git to move your commits between Puppet environments. Instead, Continuous Delivery for PE does that work for you.

Before we learn how deployments work, it's important to define some key terms.

-   **Environment node group**

    An environment is a set of nodes used for a particular purpose. An environment node group is node group you create in Puppet to represent an environment. Environment node groups define which nodes belong to which environment.

    Environment node groups can be broad, such as Staging--all nodes used to stage changes prior to shipping them to production. They can also be smaller and more specific, such as Denver Production Forge Servers. When Continuous Delivery for PE deploys changes, it deploys them to one environment node group at a time.

    A node cannot be in two environment node groups.

-   **Puppet environment**

    A Puppet environment is a Git branch that gets turned into a directory on your Puppet master.

    A Puppet environment specifies the resources that the Puppet master uses when compiling catalogs for agent nodes. The code management tools built into Puppet Enterprise create and maintain your Puppet environments based on the branches in your control repo.

    For example, if your control repo has a production branch, a development branch, and a testing branch, code management creates a production Puppet environment, a development Puppet environment, and a testing Puppet environment.


Continuous Delivery for PE manages the journey from changes made in version control to checkout in a Puppet environment to deployment on nodes in an environment node group. When you kick off a deployment, you're in essence telling Continuous Delivery for PE that "I want to deploy the changes in commit \#x on the master branch to nodes in my environment node group \#y." Continuous Delivery for PE then talks to your version control system, to Code Manager, to the node classifier, and to the orchestrator to guide and automate deployment of the change you've requested to the right group of nodes.

The basic steps in this process are as follows:

1.  You initiate a deployment and specify the commit to be deployed and the target environment node group.

2.  Continuous Delivery for PE updates the target branch and Puppet environment.

3.  The orchestrator runs Puppet against the impacted nodes to apply the code change.


## Git branches and Continuous Delivery for PE

Continuous Delivery for PE is designed to watch for Puppet code changes you make in Git and help you deploy those changes to your environment node groups. You and Continuous Delivery for PE work together, so it's important to understand what to do in Git, and what Git work Continuous Delivery for PE handles for you.

Continuous Delivery for PE automates management of Git branches for Puppet environments, letting you focus on Git workflows for developing and deploying changes to your code.

You and your team own the master branch in Git, as well as any other development-focused branches you create, such as feature, hotfix, or release branches. In simple Git workflows, the master branch is designated as the branch to file pull requests against, tag releases off of, and treat as the leading edge of new development.

It is also your responsibility to create new environment branches when you first set up environment node groups. Environment branches are long-lived Git branches used to control which version of code is made available in corresponding Puppet environments. Continuous Delivery for PE owns environment branches after they're created, deploying changes to them and keeping them up to date. You and your team won't make changes to these branches directly, because manual changes to environment branches get overwritten whenever an automated deployment occurs.

While you focus on making commits to master and getting pull requests merged, Continuous Delivery for PE works in your environment branches on your behalf, making sure your commits or changes are tested, deployed, and promoted to your environment node groups in accordance with the control repo pipelines you create and your manual deployment directives.

