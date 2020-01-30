---
layout: default
title: "Integrating with a repository"
--- 

For full continuous integration (CI) and continuous deployment (CD) you will want to integrate your repository with Pipelines. For more information on CI/CD see [Overview of CI/CD with Pipelines](./index.html).

Doing so will allow you to attach Pipelines applications to repositories and enable auto build and auto deploy features.

## Integrate a repository

The master settings for integrating with a repository can be found in your account settings. To change these settings:

1. Click on the gear icon in the top right of the Pipelines web UI.
1. Click <b>Integrations</b> link on the left.
1. Click the repository you wish to integrate with. 

    > **Note:** If you are already connected to a repository, the button will say \Disconnect REPO Account\. To disconnect (terminate) the integration, click the button. If you disconnect a repository that has repositories connected to applications in Pipelines for Applications, the applications will no longer auto build when a repository check-in occurs.

1. Click the "Connect REPO Account" button for the repository you wish to integrate with. You may be prompted to login to the repository and to approve access to the repository. If you have integrated Pipelines previously, you may not be prompted at all. 

Once you have approved the integration on the repository, they are integrated.

## Remove Pipelines integration from GitHub

To completely remove Pipelines integration with GitHub:

1. Click on the gear icon in the top right of the Pipelines web UI.
1. Click <b>Integrations</b> link on the left.
1. Select the repository you wish to remove, and click **Disconnect REPO Account**. 
1. Log in to your GitHub account and go to the **Settings** page.
1. On the GitHub settings page click the <b>Applications</b> link. Under Authorized applications, find Distelli Inc. and click the <b>Revoke</b> button.

### Remove GitHub repository webhooks or services

To remove a webhook from a specific repository in GitHub:
<ol>
<li>Log in to your GitHub account.</li>
<li>Navigate to the repository that has webhooks you wish to remove.</li>
<li>Click <b>Settings</b> icon.</li>
<li>Click the <b>Webhooks & Services</b> link.</li>
<li>Remove the hook or service you wish.</li>
</ol>

## Remove Pipelines Integration from Bitbucket

To completely remove Pipelines integration with Bitbucket:

1. Click on the gear icon in the top right of the Pipelines web UI.
1. Click <b>Integrations</b> link on the left.
1. Select the repository you wish to remove, and click **Disconnect REPO Account**.
1. Log in to your Bitbucket account and go to the Bitbucket <b>Manage account</b> page.
1. On the **Manage** page click the <b>OAuth</b> link.
1. Under OAuth integrated applications, find Distelli Inc and click the <b>Revoke</b> link.

### Remove Bitbucket Repository Webhooks or Services

To remove a webhook from a specific repository in Bitbucket:
<ol>
<li>Login to your Bitbucket account.</li>
<li>Navigate to the repository that has webhooks you wish to remove.</li>
<li>Click <b>Settings</b> icon.</li>
<li>Click the <b>Webhooks</b> or <b>Services</b> link.</li>
<li>Remove the hook or service you wish.</li>
</ol>


