---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Start setting up your workspace

As a new user, you'll need to perform some initial workspace setup tasks before you can begin using the core features of Continuous Delivery for PE. This guide will also provide our recommendations for how to think about and work with environments and Git branches as you use Continuous Delivery for PE.

## Before you begin

Make sure you're ready to get started by completing the following:

1.  Create your user account and sign into the Continuous Delivery for PE web UI. See [Creating a user account](create_a_user_account.md) for instructions.

2.  Integrate the source control system you use to store your Puppet code. See [Integrating with source control](integrations.md#) for instructions.


## Git branches and Continuous Delivery for PE

Continuous Delivery for PE is designed to watch for Puppet code changes you make in Git and help you deploy those changes to your environment node groups. You and Continuous Delivery for PE will be working together, so it's important to understand what you should do in Git, and what Git work Continuous Delivery for PE will handle for you.

Continuous Delivery for PE automates management of Git branches for Puppet environments, letting you focus on Git workflows for developing and deploying changes to your code.

You and your team will own the master branch in Git as well as any other development-focused branches you create, such as feature, hotfix, or release branches. In simple Git workflows the master branch is designated as the branch to file pull requests against, tag releases off of, and treat as the leading edge of new development.

It will also be your responsibility to create new environment branches when you first set up environment node groups. Environment branches are long-lived Git branches used to control which version of code is made available in corresponding Puppet environments. Continuous Delivery for PE will own environment branches after they're created, deploying changes to them and keeping them up to date. You and your team won't make changes to these branches directly, because manual changes to environment branches will be overwritten whenever an automated deployment occurs.

While you focus on making commits to master and getting pull requests merged, Continuous Delivery for PE will work in your environment branches on your behalf, making sure your commits or changes are tested, deployed, and promoted to your environment node groups in accordance with the control repo pipelines you create and your manual deployment directives.

## Add a module code repository

A module code repository in Continuous Delivery for PE tracks the changes made on the active development branch of your source control system. When adding a module code repository to Continuous Delivery for PE, it's important to connect the Git branch on which the module receives active development.

### About this task

When you set up your new module repo, Continuous Delivery for PE adds a webhook to the associated repository in your source control system. The webhook reports new commit activity on the repository to Continuous Delivery for PE, making it easy for you to track code changes and take action.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Modules**, then click **Add Module**.

2.  Follow the prompts to select your source control, organization, and your chosen module repository.

3.  When prompted to select a branch, choose the branch that's under active development. This should be your master branch.

    **Remember:** When working with Continuous Delivery for PE, you must only commit to the master branch and any feature branches \(which will eventually be merged back into the master branch\). Do not push code changes to any Git branches associated with Puppet environments, as doing so can create conflicts with Continuous Delivery for PE workflows.

4.  **Bitbucket Server users**: See [Configure Bitbucket Server repositories](configure_bitbucket.md#) for instructions on how to set up the two plugins required to make Continuous Delivery for PE fully operational on your repository.

5.  Edit the module's name.

6.  Click **Add**. The module is now shown in the master list on the Modules page.


### Result:

Now that we've added our first module to Continuous Delivery for PE, we can run tests on that module's code. To learn how, proceed to [Start testing module code with jobs](start_testing_module_code.md#).

