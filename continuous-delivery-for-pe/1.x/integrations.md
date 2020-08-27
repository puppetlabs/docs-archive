---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Integrate with source control

Integrate your source control system with Continuous Delivery for Puppet Enterprise \(PE\) by following the appropriate set of instructions on this page.

## Integrate with Bitbucket Server

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Integrate your Bitbucket Server instance with Continuous Delivery for PE in order to start using these tools.  

### About this task

**Note:** Continuous Delivery for PE supports Bitbucket Server 4.9.1 and newer versions.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings** ![](settings_icon.png).

2.  Click **Integrations**, then click **Bitbucket Server**.

3.  In the **Bitbucket Server Host** field, enter the public IP or DNS for your Bitbucket Server instance.

4.  In the **Username** and **Password** fields, enter the credentials associated with the account you wish to connect to Continuous Delivery for PE.

5.  In the **SSH Port** field, enter the port number on which your Bitbucket Server listens for SSH requests. To locate this port number:

    1.  In the Bitbucket Server web UI, click **Administration** \(the gear icon\) and then click **Server settings**.

    2.  Locate the SSH port in the **SSH access** section of the Server settings page.

6.  Enter the SSH base URL for your Bitbucket Server if it is different from the host URL. To view your SSH base URL:

    1.  In the Bitbucket Server web UI, click **Administration** \(the gear icon\) and then click **Server settings**.

    2.  Locate the SSH base URL in the **SSH access** section of the Server settings page.

7.  Enter the SSH user for clones if it is something other than "git."

8.  Click **Add Credentials**.


### Result:

You can now begin adding control repos and modules to your Continuous Delivery for PE instance.

Bitbucket Server requires the use of two plugins on each Bitbucket repository you use with Continuous Delivery for PE to support webhooks and pull request notifications. See [Configure Bitbucket Server repositories](configure_bitbucket.md#) for instructions on installing and enabling the two plugins.

## Integrate with GitHub

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Create a GitHub OAuth application in order to integrate your GitHub instance with Continuous Delivery for PE and start using these tools.  

### Before you begin

An administrator on your team must create a GitHub OAuth application for Continuous Delivery for PE.

1.  Sign into Continuous Delivery for PE as the root user.

2.  Click **Settings**, then click **Integrations**.

    **Tip:** The authorization callback URL required to create your OAuth app is shown in the console.

3.  In your organization's GitHub account, create an OAuth application. See [Creating an OAuth App](https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/) in the GitHub documentation for instructions.

4.  Once your OAuth application is created, note the Client ID and Client Secret shown on the application's page in the GitHub UI.

5.  Return to the Continuous Delivery for PE root console. On the Integrations page, enter the client ID and secret for your GitHub OAuth application and click **Add**.


### About this task

Once a GitHub OAuth application is established for your organization, each individual user must authenticate with the application in order to integrate their Continuous Delivery for PE instance with GitHub.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings** ![](settings_icon.png).

2.  Click **Integrations**, then click **GitHub**.

3.  Click **Add Credentials**.

    At this point you'll be redirected to GitHub to authorize the OAuth application set up by your team's administrator.

    Give Continuous Delivery for PE the following permissions on your GitHub account:

    -   Access organizations, teams, and membership \(read-only\)

    -   Access user email addresses \(read-only\)

    -   Access public and private repositories

4.  Click **Authorize application**.


### Result:

## Integrate with GitHub Enterprise

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Integrate your GitHub Enterprise instance with Continuous Delivery for PE in order to start using these tools.  

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings** ![](settings_icon.png).

2.  Click **Integrations**, then click **GitHub Enterprise**.

3.  In the **Host** field, enter the public IP or DNS for your GitHub Enterprise instance.

4.  Create a token allowing Continuous Delivery for PE to access your GitHub Enterprise instance.

    1.  In the GitHub Enterprise web UI, click your profile photo, then click **Settings**. 

    2.  Click **Personal access tokens**, and click **Generate new token**.

    3.  Enter a token description, such as CD for PE.

    4.  Select the **repo**, **read:org**, and **user:email** scopes.

    5.  Click **Generate token**.

    6.  Copy the personal access token created by GitHub Enterprise.

5.  In the Continuous Delivery for PE web UI, enter the GitHub Enterprise token in the **Token** field.

6.  Click **Connect**.


## Integrate with GitLab

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Integrate your GitLab instance with Continuous Delivery for PE in order to start using these tools.  

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings** ![](settings_icon.png).

2.  Click **Integrations**, and then click **GitLab**.

3.  In the **Host** field, enter the public IP or DNS for your GitLab instance.

4.  Create a token allowing Continuous Delivery for PE to access your GitLab instance.

    1.  In the GitLab web UI, navigate to your user settings and click **Access Tokens**.  

    2.  Enter a name for the application, such as CD for PE, and set an expiration date for the token.

    3.  Select the **api** and **read\_user** scopes.

    4.  Click **Create personal access token**.

    5.  Copy the personal access token created by GitLab.

5.  In the Continuous Delivery for PE web UI, enter the GitLab token in the **Token** field.

6.  Add the SSH user's credentials in the **SSH User** field.

7.  Click **Connect**.


