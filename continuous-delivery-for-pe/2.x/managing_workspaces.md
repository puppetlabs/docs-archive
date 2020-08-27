---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Managing workspaces

Workspaces enable you to share access to key Continuous Delivery for PE resources, such as control repos, modules, and jobs, with the other members of your team. Once you set up a workspace, add your team members to that workspace and give them the user permissions needed to do their work.

## Best practices when creating workspaces

This section offers suggestions and best practices for creating workspaces. This guidance is intended to help you and your organization understand Continuous Delivery for PE workspaces and use them effectively.

Workspaces support teams writing Puppet code and deploying that code to nodes managed by PE. Whether your organization tasks one team with writing and deploying all Puppet code, or has organized multiple teams to write and test Puppet code while a central deployment team pushes those changes to production, workspaces can help you ensure that each team member has exactly the Continuous Delivery for PE resources they need.

|If your organization uses...|Workspace recommendation|
|----------------------------|------------------------|
|**One team** to write, test, and deploy all Puppet codeA **single source control repository** to store all Puppet code

|-   Use one workspace for the whole team
-   Set permissions carefully to ensure that each team member has access to the resources they need

|
|**Multiple teams** to write and test Puppet code**Multiple source control repositories** to store Puppet code

 A **deployment team** responsible for getting Puppet code changes into production

|-   Set up separate workspaces for each writing and testing team, ideally one workspace for each control repository
-   Create a separate workspace for the deployment team, and allow these team members access to all other workspaces

-   Set permissions carefully to ensure that each team member has access to the resources they need


|

## Set up a new workspace for a team

Perform these steps to set up a single workspace for a small, centralized team, or to create one of several team workspaces for a larger, less centralized organization.

### Before you begin

Make sure all members of the team have created individual Continuous Delivery for PE accounts.

**Note:** When new users reach the Choose a workspace screen in the new account creation process, ask them to log out of Continuous Delivery for PE until the team workspace is fully set up.

### About this task

### Procedure

1.  Create a new workspace by navigating to Manage Workspaces at top of the navigation bar in the Continuous Delivery for PE web UI and clicking **+ Add New Workspace**. Give the workspace a descriptive name \(if necessary, you can change this later in the **Settings** menu\).

    When you create a workspace, you are automatically set as the owner of the workspace. Other Continuous Delivery for PE super users can also access your workspace by navigating to the root console and clicking the name of your workspace in the **Workspaces** tab.

2.  Switch to the newly created workspace by clicking its name in the list of My workspaces, or by selecting it from the workspaces menu in the Continuous Delivery for PE web UI.

3.  Follow the new workspace prompts at the top of the screen to set up the required resources for the workspace:

    1.  If necessary, [integrate Puppet Enterprise](integrate_with_puppet_enterprise.md#)

    2.  [Integrate with your source control system](integrations.md#)

    3.  [Set up job hardware for this workspace](configure_job_hardware.md#) or use [global shared job hardware](configure_job_hardware.md#)

4.  Add the control repo where your team keeps its Puppet code to Continuous Delivery for PE. Click **Control Repos**, then click **Add Control Repo**.

5.  Add the members of your team as users of the workspace. Click **Settings**, then **Users**, and add each team member.

6.  Click **Groups** and create one or more new groups, assigning appropriate permissions and users to each group. Make sure that every team member is assigned to at least one group.

    If a user is added to a workspace but not assigned to any groups, they will see a 403 error when they sign into Continuous Delivery for PE.

7.  Invite the members of the team to sign into Continuous Delivery for PE. They'll now see your newly created team workspace in the workspaces area of the Continuous Delivery for PE. web UI.


### Result:

Team members are now able to view and interact with the resources you've added to the workspace according to the permissions you've assigned.

You can delete unneeded workspaces by navigating to **Settings** \> **Workspace** and clicking **Delete Workspace**. Only the workspace owner or a super user can perform this action.

