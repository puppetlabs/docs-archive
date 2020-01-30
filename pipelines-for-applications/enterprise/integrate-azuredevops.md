---
layout: default
title: "Integrate Azure DevOps"
--- 

Integrating Azure DevOps with Pipelines for Applications is a two-phase process. First, an administrator must create an OAuth application for your organization's Azure DevOps instance. Once the OAuth application is established, each individual user must connect their Azure DevOps and Pipelines for Applications accounts. 

## Create the Azure DevOps OAuth application

An administrator on your team must create an Azure DevOps OAuth application for Pipelines for Applications. 

1. Go to [https://app.vsaex.visualstudio.com/app/register](https://app.vsaex.visualstudio.com/app/register). Enter your company name.

1. In the **Application Information** section, enter a name for your OAuth application, such as **PFA**.

1. In the **Application website** field, enter the base URL for your Pipelines for Applications instance.

1. In the **Authorization callback URL** field, enter the base base URL for your Pipelines for Applications instance followed by `/oauth2/azureDevOps`. 

   > **Example:** `https://samplecorp.pipelines.com/oauth2/azureDevOps`

1. In the **Authorized scopes** section, select **Code (read and write)**.

1. Click **Create Application**. Your new application is created, and a new page showing the application's settings is displayed.

   > **Important:** Leave this page open. You'll need the application settings information in the next step.
   
1. Sign into Pipelines for Applications as the root user.
2. Click **Settings**, then click **Integrations**.
   
1. On the Integrations page, enter the application ID and client secret for your Azure DevOps OAuth application and click **Add**.

## Integrate with Azure DevOps

> **Before you begin:** An administrator on your team must create an Azure DevOps OAuth application. 

Once an Azure DevOps OAuth application is established for your organization, each individual user must connect their Azure DevOps and Pipelines for Applications accounts. This process involves granting code read and write permissions and adding a public SSH key, which enables cloning of modules and control repos during automated tasks.

1. In the Pipelines for Applications web UI, click **Settings**.
1. Click **Integrations**, then click **Azure DevOps**.
1. Click **Connect Azure DevOps Account** to give Pipelines for Applications code read and write permissions for your Azure DevOps account. You are directed to a Microsoft page.
1. Click **Accept**. You are directed back to the **Integrations** page.
1. Next, add the SSH key. Still in the Pipelines for Applications web UI, click **SSH Keys**. If you have not already done so, generate a SSH key. 
1. Click **Show** to display your public SSH key. Copy the key.
1. In the Azure DevOps web UI, open the user menu and click **Security**, then click **SSH public keys**.
1. Click **Add** and paste your public SSH key into the **Key Data** field. Add a description and click **Save**.