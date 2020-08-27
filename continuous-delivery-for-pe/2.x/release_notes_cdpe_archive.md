---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Release notes archive

Release notes for Continuous Delivery for PE 2.x release series versions that are more than six months past the most recent 2.x release version are housed in the release notes archive.

## Version 2.9.1

Released 30 April 2019

Resolved in this release:

-   MySQL users were unable to run Continuous Delivery for PE version 2.9.0.


## Version 2.9.0

Released 30 April 2019

New in this release:

-   **Pipeline stage status shown in your source control system.** The status of each stage of the relevant pipeline is now shown alongside a commit or pull request in your source control system.

-   **Master branch creation for new modules.** When you add a new module to Continuous Delivery for PE, the software now checks to see if the module contains a master branch. If no master branch is present, Continuous Delivery for PE offers to create one for you from an existing branch of your choosing. For more on the master branch and its importance, see [Git branches and Continuous Delivery for PE](introducing_deployments.md#).

-   **Name a newly created pipeline stage.** To assign a custom name to a newly created pipeline stage, fill in the **Name** field when adding the new stage.
-   **Usability improvements.** We've made numerous small usability improvements, including:

-   If an impact analysis task fails, a **Failed** message is now shown in the pipeline and events views.

-   You can now show or hide the value of your GitHub Enterprise token when adding credentials.


Resolved in this release:

-   Due to a Firefox known issue, Firefox users saw a blank screen when opening a message in the message center.

-   When editing the later stages of a long pipeline, the editing modal often appeared above the visible area of the browser window.

-   Clicking **Edit Deployment** ![](edit.png) too soon after a new page load caused an error.

-   A non-descriptive error was shown when you attempted to deploy to a node group that didn't contain any nodes.

-   When adding new PE credentials, relevant fields were not displayed for manual entry of information not automatically discovered by Continuous Delivery for PE.

-   The **Edit Impact Analysis** screen did not accurately show the environments you chose to run impact analysis tasks on.


## Version 2.8.2

Released 25 April 2019

Resolved in this release:

-   The Continuous Delivery agent was unable to successfully clone repositories from GitHub. To fix this issue, [Update the Continuous Delivery agent](configure_job_hardware.md#) to the latest version.


## Version 2.8.1

Released 18 April 2019

Important security notice:

-   **Root passwords were exposed in the PE console for some users.** If you used the **cd4pe::root\_configuration** task to configure your Continuous Delivery for PE installation, the root user's username and password were exposed in the job's **Job Details** pane in the PE console. To fix this issue, upgrade the `puppetlabs-cd4pe` module to [version 1.2.1](https://forge.puppet.com/puppetlabs/cd4pe) and [reset the root user's password](the_root_console.md#).


## Version 2.8.0

Released 15 April 2019

Important notice for users upgrading to version 2.8.0:

-   **A Continuous Delivery agent upgrade is required on all SELinux-enabled job hardware.** This version features improved support for distribution-packaged Docker on hosts with SELinux enabled. Mounting volumes from the host to the Continuous Delivery for PE container now tells SELinux to allow the container access to the volume mount. If you have SELinux enabled, you must upgrade your Continuous Delivery agents. For instructions, see [Upgrade the Continuous Delivery agent](configure_job_hardware.md#).


New in this release:

-   **View a control repo or module's event history sorted by pipeline run.** You can now choose to view the event history for your control repo or module by pipeline run, rather than by chronological event. To get started, choose **View history by: Pipeline** on your control repo or module's **Events** pane.

    **Note:** If you're upgrading to Continuous Delivery for PE version 2.8.0, please note that we are unable to retroactively sort your event history from previous versions of the software into pipeline runs. Only pipeline runs triggered using version 2.8.0 and newer versions are shown in the pipeline view.

-   **Automatic Git branch creation when deploying a module to an environment without a matching branch.** When you deploy a module to an environment for which a matching Git branch does not yet exist, Continuous Delivery for PE now creates the branch for you.

-   **Set a deployment's timeout duration.** When creating a new deployment, you can now specify the length of the deployment's timeout period. By default, deployments time out after 60 minutes.

-   **Name your pipeline stages.** You can now provide custom names for the stages in your control repo and module pipelines. To name or rename a pipeline stage, click the **More Actions** ![](kebab.png) menu and select**Rename Stage**.

-   **Support for custom CA certificates for GitHub Enterprise.** When setting up your GitHub Enterprise integration, you now have the option to provide a custom CA certificate, such as a self-signed certificate.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   The description you provide for a manually triggered module deployment is now shown in the module's events timeline and on the deployment's details page.


Resolved in this release:

-   If you enabled SAML and then ran a configuration test, SAML was disabled until you re-saved the integration.

-   A display error occurred if you reordered the stages of a default pipeline while the pipeline contained stage placeholders.

-   When using GitHub Enterprise with self-signed certificates, an error was shown when you ran a job.


Removed in this release:

-   **Dispatch a job.** We've removed the ability to manually dispatch a job from the Jobs page.


## Version 2.7.1

Released 2 April 2019

Resolved in this release:

-   When manually triggering a regex branch pipeline for a control repo, the branch selection section of the **Run Pipeline** page was unresponsive.


## Version 2.7.0

Released 1 April 2019

New in this release:

-   **Default pipelines.** When creating a new pipeline for a control repo or module, Continuous Delivery for PE now offers to set up a default pipeline for you. Default pipelines contain recommended stages along with prompts to build out the pipeline's functionality. You can edit and rearrange the elements of the default pipeline to suit your needs. To get started, click **+ Add default pipeline** when creating a new pipeline.

-   **SSL support.** To learn how to set up SSL for your Continuous Delivery for PE instance, see [Configure SSL](configure_ssl.md#).

-   **Notification of upcoming token expiration.**You'll receive a notification in the message center ![](message.png) when yourPE token is two weeks from its expiration date, and another notification if the token expires.

-   **Updated guidance and new default queries for setting up regex branch pipelines.**These changes are designed to help you avoid accidentally triggering the regex branch pipeline when changes are deployed to your environment branches.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   You are no longer required to generate and enter a new PE token when adding or editing impact analysis credentials after integrating your PE instance.

-   Improved messaging when configuring storage in the root console.


Deprecated in this release:

-   **Incremental branch and blue-green branch deployment policies.** The incremental branch and blue-green branch deployment policies have been deprecated. These deployment policies will be removed from Continuous Delivery for PE in a future release.

-   **Support for Puppet Enterprise version 2017.3.** PE 2017.3 has reached the end of its support lifecycle.


## Version 2.6.0

Released 18 March 2019

New in this release:

-   **Module deployments.** You can now deploy module changes to your Puppet environments using your module's pipeline. In order to deploy module code, you must first add a `:branch => :control_branch` declaration to the module's section of your Puppetfile. See [Deploy module code](deploy_module.md) for more information.

-   **Master branch creation for new control repos.** When you add a new control repo to Continuous Delivery for PE, the software now checks to see if the control repo contains a master branch. If no master branch is present, Continuous Delivery for PE offers to create one for you from an existing branch of your choosing. For more on the master branch and its importance, see [Git branches and Continuous Delivery for PE](introducing_deployments.md#).

-   **No-op runs for deployments.** When creating a new deployment for a control repo, you now have the option to set the deployment to run in no-op mode. No-op deployments are not available for regex pipelines or deployments using the eventual consistency deployment policy.

-   **Redesigned** **Puppet Enterprise** **account settings page.** The **Puppet Enterprise** account settings page now displays each connected PE instance's node classifier address, number of protected environments, and token expiration date.

-   **Specify the lifetime of your** PE **token.** When adding new PE credentials using basic authorization, you can now customize the lifetime of the PE token Continuous Delivery for PE generates for you.

-   **Regenerate a PE token.** On the Puppet Enterprise account settings page, click **Regenerate** to enter a new API token or provide your PE credentials to have a new token generated for you.

-   **Run job hardware on Ubuntu 18.04.** You can now install the Continuous Delivery agent on systems running Ubuntu 18.04.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   Improved messaging when setting up a deployment using the feature branch policy.

-   No longer requiring a deployment description when adding a deployment to a pipeline stage,

-   Reorganized the steps for creating a new deployment for a more logical flow.


Resolved in this release:

-   All deployments with stagger settings ran batches of 10 nodes at a time, rather than the number of nodes specified when the deployment was created.

-   The **Update Ref** section of the deployment details page was not displayed for deployments using the eventual consistency deployment policy.

-   Terminate conditions were not shown on the deployment details page for deployments using the direct merge deployment policy.


## Version 2.5.0

Released 4 March 2019

New in this release:

-   **Deploy code from a regex branch pipeline to a feature branch Puppet environment.** You can now add a deployment to your regex branch pipeline. When triggered, this deployment uses the Feature Branch deployment policy to deploy the new code to a Puppet environment with the same name as the branch where the new code is housed. If a Puppet environment by this name doesn't exist at the time of deployment, Continuous Delivery for PE creates it for you.

-   **Manually trigger a regex branch pipeline.** You can now start a pipeline run on a regex branch pipeline by clicking **Run Pipeline** ![](trigger-pipeline.png).

-   **Onboarding guide for first-time users.** New users are now greeted by a guided path through the integrations and setup tasks required to get started with Continuous Delivery for PE.

-   **Reorder pipeline stages.** You can now change the order in which the stages in your pipeline appear. Click the **More Actions** ![](kebab.png) menu and select**Reorder Stages**.

-   **Support for pull requests from forked repositories.** The Continuous Delivery for PE pull request \(PR\) gate workflow now accepts PRs from forked repositories in GitHub, GitLab, and Azure DevOps. Pull requests from forked repositories on Bitbucket Server are not currently supported.


Resolved in this release:

-   The **New LDAP Configuration** screen's close control was not operational.

## Version 2.4.0

Released 21 February 2019

New in this release:

-   **Fewer inputs required for PE integration.** We've simplified the process for integrating your PE instance with Continuous Delivery for PE, and no longer require you to manually generate an API token, enter your endpoints, or paste in your CA certificate. See [Add your Puppet Enterprise credentials](integrate_with_puppet_enterprise.md#) for details.

-   **Regex branch pipelines.** You can now create a pipeline for branches in your control repo or module that aren't associated with an existing pipeline, and have names matching the regular expression you set. Only one regex branch pipeline is allowed per module or control repo. To create a regex branch pipeline, click **+ Add Pipeline** and select **Branch Regex**.

-   **Improved controls for adding pipeline stages.** Previously, you could add stages only to the bottom of your pipeline. This release introduces the **More Actions** ![](kebab.png) menu to every pipeline stage, which includes the options to add a stage before or after any stage.

-   **Hiera 3 support.** Continuous Delivery for PE now supports use of Hiera 3.

-   **Deployment approval notifications in the message center.** Notifications of deployments to protected environments that require approval are now shown in the message center. Members of the approval group will also continue to receive email notifications.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   Disk storage is now shown first in the list of available storage options on the **Settings** page.

-   A default name for the disk storage bucket is prepopulated when configuring disk storage.

-   Clearer controls and guidance when adding impact analysis credentials.

-   You can now delete messages from the message center.


Resolved in this release:

-   If you entered data into one of the fields on the storage settings page, then changed to a different storage type, the data was shown in the fields for the new storage type.

-   A recent breaking change to the Azure DevOps API caused new Azure DevOps integrations with Continuous Delivery for PE versions 2.3.0 and earlier to fail.


## Version 2.3.0

Released 4 February 2019

Important notice for users upgrading to version 2.3.0:

-   **A Continuous Delivery agent upgrade is required on all Docker job hardware.** Due to this version's significant improvements to job execution in Docker containers, an upgrade of the Continuous Delivery agent is required on all Docker-capable job hardware. For instructions, see [Upgrade the Continuous Delivery agent](configure_job_hardware.md#).


New in this release:

-   **Improved support for LDAP.** We've made significant improvements to the Continuous Delivery for PE LDAP integration, including the ability to integrate multiple LDAP servers, the option to perform recursive group lookups, and support for LDAP group mapping. For more information, see [Configure LDAP](configure_ldap.md#).

-   **Disk storage.** Continuous Delivery for PE now supports disk storage, eliminating the requirement to use Amazon S3 or Artifactory for external object storage of logs, job manifests, and artifacts. To use disk storage, you must mount a volume to the Continuous Delivery for PE Docker container. For more information, see [Install Continuous Delivery for PE using Docker](installing_continuous_delivery.md).

-   **Eventual consistency deployment policy.** Continuous Delivery for PE now provides the option to deploy new Puppet code without launching any orchestrator jobs. Use the eventual consistency deployment policy when you prefer to use your regularly scheduled Puppet runs to pick up the changes.

-   **Links to associated PE jobs.** A deployment's details page now displays links to the Puppet jobs that were created as part of the deployment in the PE console.

-   **Faster Docker-based jobs.** We've made significant improvements to the execution speed of jobs run in Docker containers.

-   **Docker run arguments included in Docker-based jobs.** When creating a job that runs inside a Docker container, you now have the option to pass Docker run arguments to the container. Find the optional **Docker Run Arguments** field in the **Docker Configuration** section of the **New Job** and **Edit Job** panes.

-   **Message center.** Open the message center ![](message.png) in the navigation bar to view important notifications about required actions following an upgrade ofContinuous Delivery for PE.

-   **Analytics data collection.** In order to help us better serve you, our users, Continuous Delivery for PE is now gathering information on how you work with the software. No personally identifiable information is ever collected, and the data we gather is never used or shared outside Puppet. To learn about what data we collect and find instructions for opting out, see [Analytics data collection](cd_analytics.md#).

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   When displayed, the Continuous Delivery agent secret key is now shown in a modal window.

-   Improved error messaging for attempted deployments to environment node groups with classes.

-   Your job hardware server's friendly name is shown on the job details page, rather than the server's ID.

-   An error message is shown if you attempt to add an impact analysis task to a pipeline for a PE instance on which impact analysis has not been enabled.


Resolved in this release:

-   Pre-built jobs were not displayed if you navigated to the Jobs page within 30 seconds of completing the initial setup and configuration process.

-   Changes to SMTP settings did not take effect immediately.

-   Nodes selected for a deployment did not respect all the rules of a target node group's hierarchy.


## Version 2.2.1

Released 19 December 2018

Resolved in this release:

-   An incomplete deployment policy was accidentally enabled in version 2.2.0. The full feature will be available in an upcoming version.


## Version 2.2.0

Released 17 December 2018

New in this release:

-   **Azure DevOps support.** See [Integrate with Azure DevOps](integrations.md#) for instructions on how to create an OAuth application to start using your organization's Azure DevOps repositories with Continuous Delivery for PE.

-   **Manual approval workflow for protected Puppet environments.** You can now designate protected Puppet environments and create groups of users authorized to manually approve or decline deployments to those protected environments. See [Require approval for deployments to protected Puppet environments](approval.md) for instructions on configuring and using this workflow.

-   **Configurable catalog compilation batch size for impact analysis tasks.** When setting up an impact analysis task, you can now specify how many catalogs should be compiled simultaneously.

-   **Configurable SSH port number for GitLab integrations.** When setting up your GitLab integration, you now have the option to specify a custom SSH port number.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   If you have more than one pipeline for a control repo or module, the pipeline you most recently viewed is still displayed after you refresh the page.


Resolved in this release:

-   If your session timed out during the initial installation of Continuous Delivery for PE, you were unable to complete the installation process.


## Version 2.1.1

Released 7 December 2018

Resolved in this release:

-   Any pipeline containing five or more deployment or impact analysis tasks froze when triggered.

-   An incomplete feature on the Modules page was accidentally enabled in version 2.1.0. The full feature will be available in an upcoming version.


## Version 2.1.0

Released 29 November 2018

New in this release:

-   **Support for prefixed environment names**. Continuous Delivery for PE now supports use of environment name prefixes, which are set using the `prefix` subsetting of the r10k `sources` parameter. No additional setup or configuration is required to use prefixed environment names with Continuous Delivery for PE.

-   **Enable or disable LDAP nested group search.** When configuring your LDAP single sign-on credentials, you now can choose whether to perform recursive queries to search nested LDAP groups. To change your LDAP settings, navigate to the [root console](the_root_console.md#) and click **Single Sign On**.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   Modal styling on all configuration and creation screens.

-   Validation of newly created usernames to ensure they contain only allowed characters.


Resolved in this release:

-   The Settings page in the root console crashed if your chosen storage provider, Artifactory or S3, could not be validated.


## Version 2.0.3

Released 8 November 2018

New in this release:

-   **Support for smaller screens.** The Continuous Delivery for PE web UI now scales more responsively when you work with the software on a smaller screen or in a smaller browser window.


## Version 2.0.2

Released 31 October 2018

Resolved in this release:

-   Control repos with matching names were automatically selected from all available organizations for GitHub Enterprise users.


## Version 2.0.1

Released 25 October 2018

New in this release:

-   **Gravatars.** Each user account now shows its associated [Gravatar](https://gravatar.com/) image in the navigation bar of the web UI and on the user's Profile page.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   You'll now see your changed resources first when opening an impact analysis report.

-   Clearer guidance messaging when adding impact analysis tasks to a pipeline.

-   Control repo names can now contain more than 30 characters.


Resolved in this release:

-   False positives in impact analysis reports that were shown when an `if` statement in the Puppet code was updated but the node's resources remained unchanged.

-   Pipeline run errors created when an impact analysis task was the only item in a pipeline stage and **Auto Promote** to the next stage was disabled.

-   Code Manager errors created when an impact analysis task was added to a pipeline stage containing a deployment.

-   Incomplete list of job hardware shown after an item was removed from the list of job hardware.

-   Impact analysis failure when run against an environment containing no nodes.

-   **Update Ref** field on the deployment details page shown as **Waiting** after deployment completion.


## Version 2.0.0

Released 9 October 2018

New in this release:

-   **Impact analysis.** Preview the potential impact to your infrastructure of a Puppet code change before the new code is merged. Each time a pull request is opened against your pipeline, impact analysis produces a detailed report on how the new code will impact your nodes, resources, and classes. To get started with impact analysis, see [Configure impact analysis](configure_impact_analysis.md#).

-   **Docker-based jobs.** Direct any job to run inside a Docker container on your job hardware by enabling the switch in the **Docker Configuration** section of the New Job screen and specifying the Docker image name.

-   **Pre-built jobs.** This release introduces six pre-built, Docker-based jobs for testing your control repos and modules. For new users, these jobs are available on the Jobs screen upon installation. Existing users who upgrade to version 2.0.0 can create the jobs using the [Pre-built job reference](example_jobs.md#).

-   **Rerun a job.** Click **Rerun Job** ![](rerun.png) on a control repo or module'sOverview screen or on a job's Job Details screen to run a job again on the same commit.

-   **Open virtual appliance \(OVA\).** The Continuous Delivery for PE OVA offers a simplified set up process so you can run a trial or proof of concept. See [Install the OVA](install_ova.md) for instructions. The OVA is not suitable for production use.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   The confirmation button on all editing interactions in the web UI is now **Save Changes**.

-   The `https://` prefix is allowed when entering PE service endpoints during the PE integration process.


Resolved in this release:

-   403 errors when a super user attempted to navigate to a user's account from the root console.

-   Incorrect user name in pipeline item callback URLs when created by super users in other users' accounts.

-   Failure to load the list of commits for pipelines triggered while the Edit Deployment window was open.

-   Temporary removal of multiple PE credentials when a user deleted a single PE credential.

-   Inability to scroll through lists of more than 10 modules.

-   Incorrect **Control Repo Name** label on the Add Module screen.


