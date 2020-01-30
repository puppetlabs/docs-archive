---
layout: default
title: "Enterprise release notes"
--- 

These are the release notes for enterprise edition Pipelines for Containers. For the release notes for Pipelines for Applications, see [Pipelines for Applications release notes](https://puppet.com/docs/pipelines-for-apps/enterprise/pfa-release-notes.html).

> **Note:** Beginning in April 2019, enterprise edition Puppet Pipelines is delivered as a single container that includes the enterprise editions of Pipelines for Applications and Pipelines for Containers.

## Version 4.2.3

Released 29 July 2019

This release does not include any changes to Pipelines for Containers. 

## Version 4.2.2

Released 11 July 2019

This release does not include any changes to Pipelines for Containers. 

## Version 4.2.1

Released 2 July 2019

This release does not include any changes to Pipelines for Containers. 

## Version 4.2.0

Released June 18, 2019

This release does not include any changes to Pipelines for Containers. 

## Version 4.1.0

Released April 29, 2019

Resolved in this release:

* The deprecation of Bitbucket's REST API version 1, together with some GDPR-related changes to Bitbucket's APIs, caused Pipelines actions on integrated Bitbucket repositories to fail. As a result of these changes, Bitbucket now requires Pipelines users to add an SSH key in their Bitbucket settings. 
  * For more information on the new process for integrating with Bitbucket, see [Setting up Bitbucket](./bitbucket.md). 
  * For information on the deprecation and related changes, see [Bitbucket's deprecation notice](https://developer.atlassian.com/cloud/bitbucket/deprecation-notice-v1-apis/). 
  * For information on the GDPR changes, see [Bitbucket Cloud REST APIs are changing to improve user privacy](https://developer.atlassian.com/cloud/bitbucket/bitbucket-api-changes-gdpr/).  <!--CE-1097, CE-1056, CE-1057-->

## Version 4.0.0

Released April 1, 2019

Improvements in this release:

* You can now name a Helm chart release from the Helm chart deployment screen. <!--CE-845-->
* When you attempt to delete a Helm chart, Pipelines now prevents you from deleting the chart if any of your pipelines depend on it, and lists the impacted pipelines. Remove the chart from the impacted pipelines before you delete the chart. <!--CE-891-->

Resolved in this release:

* The Helm chart page wasn't updating correctly when you deleted a deployed chart, resulting in a stuck "Deletion in progress" message. The Helm chart page now updates dynamically after deletion completes. <!--CE-33-->
* Helper text was missing from pipeline deployed Helm chart releases. If a chart is deployed from a pipeline, helper text now explains why it is unaccessible from the Helm chart page. <!--CE-424-->
* We've introduced improvements to various buttons in the Pipelines interface to make them more accessible. <!--CE-975-->
* If you had more than 500 container images, some images wouldn't show up when listed in the Pipelines interface. The Pipelines interface now lists all available images. <!--CE-1051-->

## Version 3.3.10-516353

Released February 28, 2019

Improvements in this release:

* Improved OAuth 2 handling of integrations.
* Root account users can now click **Console** to access administrative settings.
* Added validation and improved error handling for Tiller connections. When you try to connect to a Tiller instance, Pipelines now tries to connect to the instance, and immediately gives you an error message if it can't connect.  

Resolved in this release:

* Improved security around password reset emails.
* In **Builds**, trying to configure a webhook resulted in an error. Build webhooks will be added in a future release. 

## Version 3.3.9-509459

Released February 11, 2019

Improvements in this release:

* When you set up a new integration, your credentials are now tested immediately to ensure that they work before your integration can proceed.  

Resolved in this release:

* A Google library dependency was causing Helm chart deployments to fail in GKE.
* In **Integrations**, after entering your credentials and connecting to Slack, the interface read **Add to Slack** instead of **Disconnect from Slack**.  
* In **Integrations**, GitLab credentials were saved without a prompt, even when integration failed.
* After adding an integration, Pipelines sometimes sent you to the Pipelines for Applications **Integrations** screen.

## Version 3.3.8-505312

Released January 30, 2019

Resolved in this release:

* Clicking **Cancel Deployment** would not properly cancel a deployment.
* AWS Integrations were always checked by default, even when not connected or configured.
* Certificate generation failed when retrieving a Helm chart's release status.
* Exceptions generated when retrieving a Helm chart's release status were not handled by the Pipelines UI.

## Version 3.3.7-489166

Released December 18, 2018    

New in this release:

* **Workflow guidance.** The projects overview page now shows you where you are in the recommended workflow and suggests next steps.

Improvements in this release:
* In projects, the **Add Containers**, **Build**, and **Deploy** buttons now use text instead of icons.

Resolved in this release:

* An API issue was preventing integration with Puppet Insights.

## Version 3.3.6-483952

Released December 10, 2018

Resolved in this release:

* When creating a deployment, adding a port mapping with a host port caused new deployments to fail.
* On the clusters page, clicking **View Pod Logs** or **View Pod Details** caused the tooltips to persist until you refreshed the page.    
* Sometimes, deleting a pipeline would fail, leaving an empty pipeline.

## Version 3.3.5-472751

Released November 6, 2018

New in this release:

* **Kubernetes role-based access(RBAC)**. All Pipelines workflows are now RBAC-enabled. You can connect to an RBAC-enabled Kubernetes cluster with Pipelines and access pod scaling settings, secrets, and Helm chart deployments. For more information, see [Set cluster permissions](./cluster-set-permissions.html).   

Resolved in this release:

* Container build templates used a Docker flag which was removed in 17.06.
* Container deployments required the **Host port** field despite the Kubernetes API considering  this optional. You can now leave this field empty when deploying a container in Pipelines.
* When creating pod scaling settings, the **Deployment to Scale** section did not filter Kubernetes deployments by namespace.

## Version 3.3.4-468982

Released October 26, 2018

Resolved in this release:
* Notifications failed if you had more than 1000 Slack channels. Slack will now properly retrieve all records (through paging) under all circumstances.

Improvements in this release:
* Updated Slack API handling to use the newer "conversations.list" instead of the deprecated "channels.list".
* Added retry logic to Slack API handling to improve performance and decrease the risk of errors from Slack rate limiting.

## Version 3.3.3-467588

Released October 23, 2018

Resolved in this release:

* Project builds and deployments incorrectly linked to Pipelines for Applications build pages.
* Manual Helm chart promotions did not link to the chart deployments.

Improvements in this release:

* Improvements to internal Helm chart processing.
* Improvements to Helm chart error handling to prevent abnormal failures.

## Version 3.3.2-461709

Released October 9, 2018

New in this release:
* **Kubernetes role-based access(RBAC)**. You can now connect to an RBAC-enabled Kubernetes cluster with Pipelines and access deployments, jobs, and services. For more information, see [Set cluster permissions](./cluster-set-permissions.html).  

Resolved in this release:

* You could add a chart with an invalid name, but you couldn't deploy it.
* Helm chart deployment stages linked to incorrect deployment pages.
* A new container build would sometimes not trigger Helm chart deployments.
* Selecting a custom container registry caused incorrect, duplicated URLs to be generated, leading to failed builds.
* Links in the interface sometimes redirected you to Pipelines for Applications.
* Helm chart deployments to VMWare Kubernetes Engine resulted in an
  error message.

## Version 3.3.1-454158

Released 24 September 2018

New in this release:

* **New and improved REST APIs!** The initial release of **List Projects** and **List Project Events** is now available.

Resolved in this release:

* Accessing pages in **Settings** sometimes switched you from Pipelines for Containers to Pipelines for Applications.
* Adding an API endpoint would fail with an unhelpful error message if you forgot to strip `https://`. You can now use `https://`.
* Launching or syncing a new GKE cluster resulted in a "java.net.UnknownHostException" error.   
* In pipelines, the version and status of deployed Helm charts did not update after a successful build.

## Version 3.3.0-448875

Released 12 September 2018

New in this release:

* **Trigger pipeline-driven Helm chart deployments on source changes**. In a pipeline, add custom Helm charts from source control to build and deploy automatically on change. Update Helm chart values to automatically deploy new containers via Helm charts in your pipeline. 

Resolved in this release:

* Pod data in connected Kubernetes clusters could not be listed due to an issue in our Kubernetes client.
* Pipeline-based deployments, chart builds, and jobs failed under certain circumstances.
* Deleting a project container failed if any of its pipelines had a Helm chart source.
* You were unable to retrieve cluster information for VMWare Kubernetes Engine (VKE) clusters.
* You were not informed if your session had expired.
* Signing in as the root user resulted in a missing tables error.

## Version 3.2.0-440239

Released 23 August 2018

New in this release:

* **Trigger custom Helm chart builds on source changes in pipelines**. In a pipeline, add custom Helm charts from source control and build automatically on change.

Resolved in this release:

* Adding search filters on branch selection for container builds caused the branch list to be hidden.

Known issues in this release:

* Pipeline-driven chart builds do not update the frontend as expected if there are two pipelines in a project. This will be fixed in a subsequent bugfix release.

## Version 3.1.0-431790

Released 31 July 2018

New in this release:

* **Build and deploy your own custom Helm Charts from source**. In addition to deploying Helm Charts from public repos, you can now build and deploy your own custom Charts. 

## Version 3.0.23-426036

Released 19 July 2018

New in this release:

* Removed Distelli branding across the user interface. 
* When connecting to an RBAC-enabled K8s cluster, you’re now prompted to set the correct cluster permissions.
* Docker Hub credentials are now validated when you add a new container.

## Known issues

**Renamed required status check for protected branches in GitHub and GitHub Enteprise**
As part of our rebranding efforts, we renamed a required status check for protected branches in GitHub and GitHub Enterprise. Users of protected branches who have enabled this required status check must follow the instructions below to switch to the new status check. 

To fix this issue: 

1. In the GitHub or GitHub enterprise web UI, go to your repository's **Settings** and click **Branches**, then click **Edit** next to your protected branch.
1. In the status checks area, deselect **continuous-integration/distelli** and select **continuous-integration/puppet**, then save your changes. 
