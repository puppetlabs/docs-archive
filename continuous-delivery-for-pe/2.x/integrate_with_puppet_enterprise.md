---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Integrate with Puppet Enterprise

To set up an integration between your Puppet Enterprise \(PE\) instance and Continuous Delivery for PE, you must first set up a dedicated PE user with appropriate permissions, then add your PE instance's credentials to Continuous Delivery for PE.

## About this task

## Result:

## Create a Continuous Delivery user and user role in PE

Create a "Continuous Delivery" user and user role in PE. This allows you to view a centralized log of the activities Continuous Delivery for PE performs on your behalf. You'll also use this user account when generating the PE authentication token required by the setup process.

### About this task

### Procedure

1.  To begin, create a new user. In the PE console, click **Access control** \> **Users**.

2.  Enter a full name \(such as Continuous Delivery User\) and a login name \(such as cdpe\_user\) and click **Add local user**.

3.  Next, create a user role containing the permissions the Continuous Delivery User needs when operating Continuous Delivery for PE. In the PE console, click **Access control** \> **User roles**.

4.  Enter a name and \(optional\) description for new role, such as CDPE User Role, then click **Add role**.

5.  Select the user role you've just created from the list on the User roles page.

6.  Click **Permissions**. Assign the following permissions to the user role:

    |Type|Permission|Object|
    |----|----------|------|
    |Job orchestrator|Start, stop, and view jobs|-|
    |Node groups|Create, edit, and delete child groups|All|
    |Node groups|View|All|
    |Node groups|Edit configuration data|All|
    |Node groups|Set environment|All|
    |Nodes|View node data from PuppetDB|-|
    |Puppet agent|Run Puppet on agent nodes|-|
    |Puppet environment|Deploy code|All|
    |Puppet Server **Note:** This permission is only available in PE 2019.1.x and newer versions. If you are using an older version of PE, you do not need to set this permission.

|Compile catalogs for remote nodes|-|

7.  Once all the permissions have been added, click **Commit changes**.

8.  Add your Continuous Delivery user to the user role. Click **Member users**. Select the name of the user you created earlier, and click **Add user**, then commit your change.

9.  Your user is now set up and has been given the permissions needed to operate Continuous Delivery for PE. Before proceeding, create a password for the Continuous Delivery user.

    1.  Return to the **Users** page.

    2.  Find and click the full name of your newly created user, then click **Generate password reset**.

    3.  Follow the link created and create a password for the user. You'll use this password when adding your PE credentials to Continuous Delivery for PE.


### Result:

## Add your Puppet Enterprise credentials

Establishing an integration with your Puppet Enterprise \(PE\) instance allows Continuous Delivery for PE to work with PE tools such as Code Manager and the orchestrator service to deploy Puppet code changes to your nodes.

### About this task

If necessary, you can add multiple PE instances to your Continuous Delivery for PE installation.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  Click **Puppet Enterprise**. Click **Add Credentials**.

3.  In the New Puppet Enterprise Credentials pane, enter a unique friendly name for your Puppet Enterprise installation.

    If you need to work with multiple PE installations within Continuous Delivery for PE, these friendly names help you to differentiate which installation's resources you're managing, so choose them carefully.

4.  Enter the web address you use to access the PE console. This address must match the certname of your PE master or an alias included in the `dns_alt_names` entry in your `puppet.conf` file.

5.  Select **Basic Authorization** or **API Token** and enter the required information:

    -   For **Basic Authorization**, enter the username and password for your "Continuous Delivery" user. Continuous Delivery for PE uses this information to generate an API token for you. The username and password are not saved. Optionally, change the token's lifetime by clicking **Edit** ![](edit.png).

    -   For **API Token**, generate a PE access token for your "Continuous Delivery" user using `puppet-access` or the RBAC v1 API, and paste it in the **API Token** field.

        For instructions on generating an access token, see [Token-based authentication](https://puppet.com/docs/pe/latest/rbac/rbac_token_auth_intro.html).

        **Tip:** To avoid unintended service interruptions, create an access token with a multiyear lifespan.

6.  Click **Save Changes**.


### Result:

Continuous Delivery for PE uses the information you provide to look up the endpoints for PuppetDB, Code Manager, orchestrator, and node classifier, and to access the master SSL certificate generated during PE installation. Once your credentials are successfully added, click **Edit Credentials** ![](edit.png) to view this information.

Your PE instance is now integrated with Continuous Delivery for PE.

If you're using PE version 2019.1.x or 2019.2.x, impact analysis was automatically configured for you during this integration. For users of all older PE versions: to enable impact analysis for this instance, see [Configure impact analysis](configure_impact_analysis.md#).  

