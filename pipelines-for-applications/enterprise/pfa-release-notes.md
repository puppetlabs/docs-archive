---
layout: default
title: "Release notes"
--- 

These are the release notes for enterprise edition Pipelines for Applications. For the release notes for Pipelines for Containers, see [Pipelines for Containers release notes](https://puppet.com/docs/pipelines-for-apps/enterprise/pfc-release-notes.html).

> **Note:** Beginning in April 2019, enterprise edition Puppet Pipelines is delivered as a single container that includes the enterprise editions of both Pipelines for Applications and Pipelines for Containers.  

## Version 4.2.3

Released 29 July 2019

Resolved in this release: 

* Some user group members were able to perform actions beyond the scope of their RBAC group's assigned permissions. <!--PIPELINES-1190-->
* A Bitbucket API compatibility issue. <!--PIPELINES-1191-->

## Version 4.2.2

Released 11 July 2019

Resolved in this release:

* Bitbucket webhooks were not firing correctly. <!--PIPELINES-1184-->
* You were unable to successfully switch from **Build on my own hardware** to **Build on shared hardware**. <!--PIPELINES-1182-->
* The **Create a free account** link was absent from the login page. <!--PIPELINES-1185-->

## Version 4.2.1

Released 2 July 2019

Resolved in this release:

* This release includes a fix for a security issue. 

## Version 4.2.0

Released 18 June 2019

New in this release:

* **Scheduled deployments.** You can now specify one-time or recurring deployment timeframes or blackouts to be applied across Pipelines for Applications. Whenever a schedule is in effect, a clock icon appears beside the name of any impacted environment. Click **Schedules** in the Pipelines for Applications web UI to get started. 
* **Custom build images.** When building an application with Docker, you can specify which build image registry you wish to use from among Docker Hub, Docker Private Registry, ECR Container Registry, or Google Container Registry. 
* **Agent download improvements.** When you download the Pipelines agent, you'll now benefit from automatic data migrations and embedded agent endpoints.
* **Streamlined root console access for super users.** Super users can now click the **Console** button in the web UI to access the root console, rather than editing the URL. 
* **Usability improvements.** We've made numerous small usability improvements, including:
  * You can now use the **Enter** key to complete the new user registration form.
  * View which version of enterprise edition Pipelines you're using in the root console.   

Issues resolved in this release:

* An infinite loop occurred if no key servers were available during a deployment.
* Branches are now automatically unpinned before deletion. <!--PIPELINES-1117-->
* In some cases, build servers were not properly enabled on the server details page. <!--PIPELINES-1098-->

## Version 4.1.0

Released 29 April 2019

Issues resolved in this release:

* The deprecation of Bitbucket's REST API version 1, together with some GDPR-related changes to Bitbucket's APIs, caused Pipelines actions on integrated Bitbucket repositories to fail. As a result of these changes, Bitbucket now requires Pipelines users to add an SSH key in their Bitbucket settings. 
  * For more information on the new process for integrating with Bitbucket, see [Integrate Bitbucket](.integrate-bitbucket.md). 
  * For information on the deprecation and related changes, see [Bitbucket's deprecation notice](https://developer.atlassian.com/cloud/bitbucket/deprecation-notice-v1-apis/). 
  * For information on the GDPR changes, see [Bitbucket Cloud REST APIs are changing to improve user privacy](https://developer.atlassian.com/cloud/bitbucket/bitbucket-api-changes-gdpr/).  <!--CE-1097, CE-1056, CE-1057-->
* A display issue that prevented you from successfully paging through all servers involved in a deployment. <!--PFA-843-->

## Version 4.0.0

Released 1 April 2019

New in this release:

* **Combined Puppet Pipelines container.** Version 4.0.0 introduces single-container delivery of enterprise edition Pipelines for Applications and Pipelines for Containers. For installation or upgrade instructions, see [Installing Pipelines Enterprise](./install-setup.html).  
* **Usability improvements.** We've made numerous small usability improvements, including:
  * Support for use of RBAC groups when using an LDAP integration. <!--PFA-701-->
  * More helpful messaging for users whose AWS tokens have expired. <!--PFA-422-->

Issues resolved in this release:

* Minor display issues when creating a new application for Bitbucket Server users. <!--PFA-543--> 

Deprecated in this release:

* Pipelines for Applications no longer supports build instructions in `travis.yml` format. <!--PFA-499-->

## Version 3.4.2

Released 1 March 2019

New in this release:

* **Designate build servers and set build server capabilities in distelli.yml.** You can now add optional **BuildCapabilities:** and **IsBuildServer:** fields to your distelli.yml file. These fields automatically mark a server on which you're installing the Pipelines agent as a build server, and set any relevant capabilities. For more information, see [Using the distelli.yml file](./distelliyml.html). <!--PFA-715 and PFA-403-->
* **Better allocation of builds to available build servers.** We've made improvements to the process Pipelines for Applications uses to assign queued builds to available build servers. <!--PFA-154-->
* **Usability improvements.** We've made numerous small usability improvements, including:
  * Improved legibility for errors on the build details and job details pages. <!--PFA-116--> 
  * A simplified workflow for adding Enterprise group privileges, including the ability to select multiple privileges at a time. <!--PFA-538 and PFA-540--> 
  * Improved messaging for approvals that cancel pending approvals to the same environment. <!--PFA-609-->

Issues resolved in this release:

* Certain pipeline prerequisites related to concurrent deployments to the same environment caused cascading deployment aborts. <!--PFA-732-->
* The **Enable restricted approval** switch displayed green when disabled and grey when enabled. <!--PFA-601-->
* The Pipelines Dashboard crashed when a snapshot was deployed to an environments with approval requirements. <!--PFA-483-->

## Version 3.4.1

Released 29 January 2019

Issue resolved in this release:

* **Failed GitHub Enterprise authentications.** A breaking change included in GitHub Enterprise version 2.16 caused Pipelines for Applications to be unable to authenticate with GitHub Enterprise.  

## Version 3.4.0

Released 25 January 2019

New in this release:

* **Updated pricing model.** Beginning with this release, enterprise edition Pipelines for Applications has transitioned to a per-user pricing model. For additional information, contact your Pipelines for Applications sales representative. 
* **Approved deployment workflow improvements.** When an application is approved for deployment to an environment, any previous releases of the application to that environment which are currently awaiting approval are automatically cancelled. <!--PFA-688-->
* **Accounts page filters.** You can now filter the **Accounts** page in the administrator console to generate lists of all super users or all inactive user accounts. <!--PFA-529: This was completed in a 3.3.4 sprint, but is feature flagged until backend work is finished.-->
* **Streamlined interface for viewing and editing user account details.** Click **View User Details** on the **Accounts** page in the administrator console to view a user's agent credentials and API token information. Click **Edit User** to update a user's password, email, allowed number of concurrent builds, or super user status. <!--PFA-529: See above.-->
* **Azure DevOps supported as a build and repository provider.** You can now set Azure DevOps as the provider of your build and repository when working with the Pipelines for Applications API or CLI. <!--PFA-206-->
* **Usability improvements.** We've made numerous small usability improvements, including:
  * If a deployment fails, a new **Restart Deployment** button is shown on the deployment details page. <!--PFA-605-->
  * When a user's password is entered incorrectly too many times, their account is locked for two hours (reduced from eight) as a security precaution. <!--PFA-674-->
  
Issues resolved in this release:

* When used in a template, DISTELLI_DOCKER_VAR environment variables did not correctly populate to the manifest. <!--PFA-153-->
* Usernames containing uppercase characters or underscores could not connect to Bitbucket. <!--PFA-579-->
* Headers used in the [puppetlabs/pipelines module](https://forge.puppet.com/puppetlabs/pipelines) caused Puppet Enterprise to report intentional change events even when the module's code had not changed. <!--PFA-656-->

## Version 3.3.4.485552

Released 10 December 2018

New in this release:

* **Azure DevOps support.** See [Integrate Azure DevOps](./integrate-azuredevops.html) for instructions on how to create an OAuth application to start using your organization's Azure DevOps code repositories with Pipelines for Applications.

## Version 3.3.4

Released 6 December 2018

New in this release:
   
* **Usability improvements.** We've made numerous small usability improvements, including:
  * Changes to the group permissions workflow that make it easier to understand which permissions are already granted to a group. <!--PFA-445-->
  * More consistent and intuitive icons in the group permissions workflow. <!--PFA-539 and PFA-449-->
  * Improved Artifactory setup and API token generation guidance for new users. <!--PFA-64-->

Issues resolved in this release:

* Merge requests submitted in GitLab were not displayed in the in the Pipelines for Applications web UI and did not automatically trigger builds. <!--PFA-541-->

## Version 3.3.3

Released 8 November 2018

New in this release:

* **Configurable installation directory location for Pipelines agent.** You can now specify a non-default installation directory for the Pipelines agent. For more information, see the [agent installation instructions](./agent.html). <!--PFA-474-->
* **Usability improvements.** We've made numerous small usability improvements, including:
  * A success message when a new job is created. <!--PFA-389-->
  * Improved functionality for the **Abort Deployment** control. <!--PFA-443-->
  * The ability to select a specific release when manually promoting a build. <!--PFA-502-->
  * A confirmation prompt when cancelling an approved deployment. <!--PFA-485-->
  * User groups with the permission to deploy an application are automatically granted the permission to view that application's deployment details. <!--PFA-480-->

Issues resolved in this release: 

* Only 100 Amazon Elastic Container Registry (ECR) results were shown in the **ECR Container Registry** field when building a new application with Docker. <!--PFA-510-->
* When a server with the Pipelines agent installed was rebooted, the Pipelines agent initiated a redundant deploy of an active release to an environment. <!--PFA-477-->
* The **Delete Job** button was intermittently non-responsive. <!--PFA-467-->
* You could not deselect **Auto Build** when creating a new application. <!--PFA-250-->
* When promoting a build, the most recent release for the repository as a whole was automatically selected, rather than the most recent release for your chosen branch. <!--PFA-501--> 
* Custom release versions did not function properly on Windows machines. <!--PFA-484-->
* You could inadvertently trigger multiple promotions by repeatedly clicking the **Promote** button. <!--PFA-487-->
* Users lacking "deploy" permissions could launch deployments in some circumstances. <!--PFA-481--> 
* Bitbucket errors caused by expired access tokens were not handled gracefully. <!--PFA-476-->
* Commit messages for releases created using a Jenkins integration were not shown in the Pipelines web UI. <!--PFA-459-->
* The list of available group permissions included blank and redundant list items. <!--PFA-433 and PFA-450-->

## Version 2.1.04

Released 27 July 2018

New in this release:

* **Custom release versions.** Designate a customized version number for a release instead of a default Pipelines version number by adding a `CustomVersionCmd:` section to your `distelli-manifest.yml` file.
* **Pin a pipeline branch.** If you have a pipeline with multiple branches, pin the branch you work with most frequently so that it is shown by default whenever you view your pipeline. 
* **Redesigned deployment interface.** We've updated the deployment workflow to make it more streamlined and consistent, and equipped it to pre-fill variables (such as task type and environment names) where appropriate. 

## Known issues

### Renamed required status check for protected branches in GitHub and GitHub Enteprise
As part of our rebranding efforts, we renamed a required status check for protected branches in GitHub and GitHub Enterprise. Users of protected branches who have enabled this required status check must follow the instructions below to switch to the new status check. 

To fix this issue: 

1. In the GitHub or GitHub enterprise web UI, go to your repository's **Settings** and click **Branches**, then select your protected branch.
1. In the status checks area, deselect **continuous-integration/distelli** and select **continuous-integration/puppet**, then save your changes.