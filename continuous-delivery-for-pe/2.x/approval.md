---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Require approval for deployments to protected Puppet environments

If your organization's business processes require manual review and approval before Puppet code is deployed to certain environments, set up an approval group of individuals with the authority to provide the needed review and sign-off. These approvers are contacted each time a deployment to a protected environment is proposed.

## Before you begin

Make sure SMTP has been configured for your Continuous Delivery for PE installation. For instructions, see [Configure SMTP](configure_smtp.md).

## About this task

Enabling a manual approval checkpoint on deployments to protected Puppet environments is a two-step process. First, designate the Continuous Delivery for PE users with the authority to approve or reject deployment requests. Next, designate the Puppet environments that require manual deployment approval.

**Important:** Designating approvers requires super user permissions.

## Procedure

1.  Create an approval group. The members of this group review all proposed deployments to the environments you designate as protected and manually approve or decline each deployment.

    1.  In the Continuous Delivery for PE web UI, click **Settings**.

    2.  In the **Groups** tab, click **+ Create Group**.

    3.  Enter a name \(such as Approval\) and description for your new group, then click **Create Group**.

    4.  Click **Go to Groups** and click **View Group Details** ![](view_details.png) for the group you just created.

    5.  In each permissions category, click **+ Set Permissions**. Select the permissions you wish to assign to the approval group and click the blue **Set Permissions** button.

        **Important:** At a minimum, the approval group must have the **List** permission for **Control Repos** in order to view and approve or deny deployments.

    6.  In **Group Members** column, add the individuals with the authority to approve or deny deployments to protected environments.

        -   To add a new member to the group, click **+ Add Member**, and search for the user you wish to add by username or email address. When you locate the user you wish to add, click **Add User**.

        -   To remove an existing member from the group, click **Remove Group Member** ![](remove_user_icon.png) and confirm your action.

2.  Designate which Puppet environments require deployment approval.

    1.  Click the **Puppet Enterprise** tab.

    2.  Click the number \(likely "0"\) in the **Protected Environments** column for your PE instance.

    3.  Select the Puppet environment that requires deployment approval.

    4.  Select the approval group you created in step 1.

    5.  Click **Add**.

    6.  If necessary, repeat these steps to designate additional environments as protected, then click **Done**.


## Result:

Now that this set-up process is complete, each time a deployment to the protected environment is triggered, either manually or through a pipeline run, the members of the approval group receive an email and a message in the message center alerting them that approval of the deployment is required.

A member of the approval group must review the deployment's details page and click **Provide Approval Decision**. After they approve or decline the deployment, a record of their decision is added to the deployment's details page.

