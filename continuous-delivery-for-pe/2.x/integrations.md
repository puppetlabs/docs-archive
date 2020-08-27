---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Integrate with source control

Integrate your source control system with Continuous Delivery for Puppet Enterprise \(PE\) by following the appropriate set of instructions on this page.

## Integrate with Azure DevOps

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Create an Azure DevOps OAuth application in order to integrate your Azure DevOps instance with Continuous Delivery for PE and start using these tools.

### Before you begin

An administrator on your team must create an Azure DevOps OAuth application for Continuous Delivery for PE.

1.  Sign into Continuous Delivery for PE as the root user.

2.  Click **Settings**, then click **Integrations**.

    **Tip:** The authorization callback URL required to create your OAuth app is shown in the root console.

3.  Go to [https://app.vsaex.visualstudio.com/app/register](https://app.vsaex.visualstudio.com/app/register). Enter your company name.

4.  In the **Application Information** section, enter a name for your OAuth application, such as CD for PE.

5.  In the **Application website** field, enter the base URL for your Continuous Delivery for PE instance.

6.  In the **Authorization callback URL** field, enter the authorization callback URL printed in the root console.

7.  In the **Authorized scopes** section, select **Code \(read and write\)**.

8.  Click **Create Application**. Your new application is created, and a new page showing the application's settings is displayed.

**Important:** Leave this page open. You'll need the application settings information in the next step.

9.  Return to the Continuous Delivery for PE root console. On the Integrations page, enter the application ID and client secret for your Azure DevOps OAuth application and click **Add**.


### About this task

Once an Azure DevOps OAuth application is established for your organization, each workspace must be authenticated with the application in order to integrate the Continuous Delivery for PE instance with Azure DevOps. This process involves granting code read and write permissions and adding a public SSH key, which enables cloning of modules and control repos during automated tasks.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  Click **Source Control**, then click **Azure DevOps**.

3.  Click **Add Credentials** to give Continuous Delivery for PE code read and write permissions for your Azure DevOps account. You are directed to a Microsoft page.

4.  Click **Accept**. You are directed back to the Source Control page.

5.  Next, add the SSH key. Still in the Continuous Delivery for PE web UI, click **SSH Key**.

6.  Click **Show** to display your public SSH key. Click **Copy**.

7.  In the Azure DevOps web UI, open the user menu and click **Security**, then click **SSH public keys**.

8.  Click **Add** and paste your public SSH key into the **Key Data** field. Add a description and click **Save**.


## Integrate with Bitbucket Cloud

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Create a Bitbucket Cloud OAuth application in order to integrate your Bitbucket Cloud instance with Continuous Delivery for PE and start using these tools.

### Before you begin

An administrator on your team must create a Bitbucket Cloud OAuth consumer for Continuous Delivery for PE.

1.  Sign into Continuous Delivery for PE as the root user.

2.  Click **Settings**, then click **Integrations**.

    **Tip:** The authorization callback URL required to create your OAuth consumer is shown in the root console.

3.  In your organization's Bitbucket Cloud account, create an OAuth consumer. See [Create a consumer](https://confluence.atlassian.com/bitbucket/oauth-on-bitbucket-cloud-238027431.html#OAuthonBitbucketCloud-Createaconsumer) in the Bitbucket Cloud documentation for instructions.
4.  When your OAuth application is created, note the key and secret shown on the OAuth settings page in the Bitbucket Cloud web UI.
5.  Return to the Continuous Delivery for PE root console. On the Integrations page, enter the client ID \(key\) and client secret for your Bitbucket Cloud OAuth consumer and click **Add**.


### About this task

Once a Bitbucket Cloud OAuth application is established for your organization, each workspace must be authenticated with the application in order to integrate the Continuous Delivery for PE instance with Bitbucket Cloud.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  Click **Source Control**, then click **Bitbucket Cloud**.

3.  Click **Add Credentials** to give Continuous Delivery for PE code read and write permissions for your Azure DevOps account. You are directed to a Microsoft page.

4.  Click **Add Credentials**.

    At this point you'll be redirected to Bitbucket Cloud to authorize the OAuth application set up by your workspace administrator.

    Give Continuous Delivery for PE the following permissions on your Bitbucket Cloud account:

    -   Access organizations, teams, and membership \(read-only\)

    -   Access user email addresses \(read-only\)

    -   Access public and private repositories

5.  Click **Authorize application**.


## Integrate with Bitbucket Server

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Integrate your Bitbucket Server instance with Continuous Delivery for PE in order to start using these tools.  

### About this task

**Note:** Continuous Delivery for PE supports Bitbucket Server 5.0 and newer versions.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  Click **Source Control**, then click **Bitbucket Server**.

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

Once a GitHub OAuth application is established for your organization, each workspace must be authenticated with the application in order to integrate the Continuous Delivery for PE instance with GitHub.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  Click **Source Control**, then click **GitHub**.

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

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  Click **Source Control**, then click **GitHub Enterprise**.

3.  In the **Host** field, enter the public IP or DNS for your GitHub Enterprise instance.

4.  Create a token allowing Continuous Delivery for PE to access your GitHub Enterprise instance.

    1.  In the GitHub Enterprise web UI, click your profile photo, then click **Settings**. 

    2.  Click **Personal access tokens**, and click **Generate new token**.

    3.  Enter a token description, such as CD for PE.

    4.  Select the **repo**, **read:org**, and **user:email** scopes.

    5.  Click **Generate token**.

    6.  Copy the personal access token created by GitHub Enterprise.

5.  In the Continuous Delivery for PE web UI, enter the GitHub Enterprise token in the **Token** field.

6.  Based on your GitHub Enterprise configuration, select either **This instance uses a standard CA certificate** or **This instance uses a custom CA certificate**. If you're using a custom certificate, paste the certificate in full in the **Custom CA Certificate** field.

7.  Click **Add Credentials**.


## Integrate with GitLab

Continuous Delivery for PE works with your existing source control system to track changes to your Puppet code and manage code deployments to your nodes. Integrate your GitLab instance with Continuous Delivery for PE in order to start using these tools.

### Procedure

1.  **Important:** In order for Continuous Delivery for PE to work correctly with GitLab, make sure that none of your environment branches \(the branches Continuous Delivery for PE works with when deploying new code to nodes\) are GitLab protected branches.

2.  In the Continuous Delivery for PE web UI, click **Settings**.

3.  Click **Source Control**, and then click **GitLab**.

4.  In the **Host** field, enter the public IP or DNS for your GitLab instance.

5.  Create a token allowing Continuous Delivery for PE to access your GitLab instance.

    1.  In the GitLab web UI, navigate to your user settings and click **Access Tokens**.

    2.  Enter a name for the application, such as CD for PE, and set an expiration date for the token.

    3.  Select the **api** and **read\_user** scopes.

    4.  Click **Create personal access token**.

    5.  Copy the personal access token created by GitLab.

6.  In the Continuous Delivery for PE web UI, enter the GitLab token in the **Token** field.

7.  Add the SSH user's credentials in the **SSH User** field.

8.  In the **SSH Port** field, specify the port on which your GitLab server listens for SSH requests. The default port number is 22.

9.  Click **Add Credentials**.


