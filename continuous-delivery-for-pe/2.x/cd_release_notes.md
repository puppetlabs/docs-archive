---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Continuous Delivery for PE release notes

These are the new features, enhancements, resolved issues, and deprecations for the Continuous Delivery for Puppet Enterprise 2.x release series.

## Version 2.18.4

Released 23 January 2020

New in this release:

-   **Fewer stacktrace exceptions included in log files.** We've reduced the number of stacktrace exceptions that resulted from checking for dependencies and approvals. You'll no longer see long stacktrace errors for the following:

    ```
    com.puppet.pipelines.cdpe.cdpeTaskUtils.CDPETaskInterruptedException: Dependency check attempt maxtime exceeded.
    com.puppet.pipelines.cdpe.cdpeTaskUtils.CDPETaskInterruptedException: Approval check attempts maxiumum exceeded. Thread should yeild and try again.
    ```


## Version 2.18.3

Released 31 October 2019

Resolved in this release:

-   This release contains internal component upgrades that resolve high-risk vulnerabilities. For a complete list of upgraded components and resolved CVEs, see the [CD4PE October 2019 Security Fixes](https://puppet.com/security/cve/cd4pe-october-2019-security-fixes) page.

## Version 2.18.2

Released 22 October 2019

Resolved in this release:

-   This release contains internal component upgrades that resolve several high-risk vulnerabilities. For a complete list of upgraded components and resolved CVEs, see the [CD4PE October 2019 Security Fixes](https://puppet.com/security/cve/cd4pe-october-2019-security-fixes) page.

## Version 2.18.1

Released 16 October 2019

Resolved in this release:

-   Bitbucket Server push and pull request webhooks were not processed correctly.

## Version 2.18.0

Released 16 September 2019

New in this release:

-   **Impact analysis reports for module code.** Preview the potential impact to your infrastructure of a Puppet module code change before the new code is merged. After you add an impact analysis task to your module pipeline, a detailed report is generated each time the pipeline is triggered, showing how the new module code will impact your nodes and resources. To get started, see [Add impact analysis to a module pipeline](impact_analysis.md#).

    **Note:** Impact analysis reports for module code are supported on PE 2018.1.9 and newer versions in the 2018.1 series, and PE 2019.1.1 and newer versions in the 2019.1 series.


## Version 2.17.1

Released 3 September 2019

Resolved in this release:

-   Module deployments could not be edited.

## Version 2.17.0

Released 3 September 2019

New in this release:

-   **Redesigned pipeline stage creation workflow.** We've updated and improved the processes for adding a new stage to your pipeline and adding new items to a pipeline stage. You no longer need to close and reopen the pipeline editing modal to add additional stages or items, and you can now add multiple jobs at once.
-   **Usability improvements.** We've made numerous small usability improvements, including:
    -   The option to set a job to run on global shared job hardware, even if global shared job hardware is not available for your installation when the job is created.
    -   When a change is manually promoted in a pipeline, the commit message is displayed.

Resolved in this release:

-   SSH private keys for each workspace were not encrypted in the database.
-   The deployment details page was not generated for manually triggered deployments.
-   An error message was shown when you attempted to rename a workspace.
-   When you added an impact analysis task to a pipeline, environments that do not support impact analysis were automatically selected.
-   The stage number was incorrectly displayed when adding a deployment to a default pipeline.
-   The full Continuous Delivery agent version number was not shown on the Job Hardware page or returned when you ran `distelli agent version`. Version 3.70.2 of the Continuous Delivery agent includes a fix for this issue.

Removed in this release:

-   **Support for Puppet Enterprise version 2018.0.** PE 2018.0 has reached the end of its support lifecycle.

## Version 2.16.0

Released 19 August 2019

New in this release:

-   **Pipeline events summary on the control repo deployment details page.** To make it easier for deployment approvers to review the validation events performed on a proposed change, the new **What's happened in the pipeline so far?** section summarizes the successes and failures in each completed pipeline stage and includes the option to view further details.
-   **Impact analysis reports include node names.** Impact analysis reports now show the names of the nodes impacted by a proposed change.

Resolved in this release:

-   During the creation of LDAP group mappings, RBAC groups were not listed for renamed workspaces.
-   The **Set up Continuous Delivery for PE** banner did not show that source control had been integrated after a Bitbucket Cloud instance was added to Continuous Delivery for PE.
-   An inaccurate message about missing PE credentials was shown when you tried to add impact analysis to a pipeline before creating a deployment.
-   In some cases, webhook auto-creation failed for Bitbucket Server.

## Version 2.15.1

Released 6 August 2019

Resolved in this release:

-   Deployments from Bitbucket Cloud failed for target branches that did not exist because Continuous Delivery for PE did not automatically create a target branch in these cases.

## Version 2.15.0

Released 5 August 2019

New in this release:

-   **Bitbucket Cloud support.** See [Integrate with Bitbucket Cloud](integrations.md#) for instructions on how to create an OAuth application to start using your organization's Bitbucket Cloud repositories with Continuous Delivery for PE.
-   **Delete workspaces.** A workspace's owner can now delete the workspace by visiting the **Workspace** tab in **Settings**. Super users can also delete workspaces.

Resolved in this release:

-   Blank rows appeared in the workspaces menu.
-   When configuring impact analysis after adding PE credentials on the New Deployment screen, a `PE credentials with the specified name already exist.` error was shown.


## Version 2.14.2

Released 29 July 2019

Resolved in this release:

-   The prefixed environment selection option did not appear for users with more than one PE instance integrated with Continuous Delivery for PE.

## Version 2.14.1

Released 24 July 2019

Resolved in this release:

-   If your trial or production license expired, you were unable to successfully upload a new license.

## Version 2.14.0

Released 22 July 2019

New in this release:

-   **Redesigned manual deployment workflow**. When you create a new manual deployment for control repos and modules, Continuous Delivery for Puppet Enterprise no longer prompts you to select a branch. The branch for your new deployment now automatically matches the pipeline you've selected. To override this setting, click **Edit** and select a new branch name.
-   **Usability improvements.** We've made numerous small usability improvements, including:
    -   A larger log window on the **Job Details** page, allowing you to see more information before you have to scroll.

Resolved in this release:

-   If you had only one PE instance integrated with Continuous Delivery for PE, and that PE instance contained prefixed environments, the prefixed environments did not appear when you created a deployment.
-   Some buttons on the SAML, LDAP, and SSL configuration screens were blank.
-   The option to add impact analysis task to a regex branch pipeline was included erroneously, and has been removed.
-   Super users could not install global shared job hardware.

## Version 2.13.4

Released 16 July 2019

Resolved in this release:

-   In automated runs, module deployments to prefixed environments resulted in errors.

## Version 2.13.3

Released 16 July 2019

Resolved in this release:

-   Performing a manual module deployment to prefixed environments resulted in errors.

## Version 2.13.2

Released 15 July 2019

Resolved in this release:

-   In certain cases, impact analysis incorrectly reported that resources had changed.

## Version 2.13.1

Released 12 July 2019

Resolved in this release:

-   If any catalog did not successfully compile during an impact analysis task, the task failed.
-   The presence of certain parameter value types in impact analysis reports caused page crashes.
-   Changes to data types marked sensitive were shown as modified in some impact analysis reports.


## Version 2.13.0

Released 11 July 2019

New in this release:

-   **Redesigned impact analysis reports.** We've updated the format of impact analysis reports to improve usability and provide you with greater detail about how the resources on your nodes will be impacted as a result of code changes.
-   **Generate impact analysis reports on demand.** You can now generate an impact analysis report for any change by clicking **New Impact Analysis** ![](impact-analysis.png) on a control repo's details page.
-   **Trial mode.** When you set up Continuous Delivery for PE for the first time, you now have the option to select **Trial Mode**, which launches a seven-day trial period without requiring a license. At the end of the trial period, you are prompted to generate and upload a free 30-day trial license.
-   **Usability improvements.** We've made numerous small usability improvements, including:
    -   When logging back into Continuous Delivery for PE, you are automatically returned to the workspace you last used.
    -   When an upstream pipeline dependency fails, downstream jobs and deployments are now labeled **canceled** instead of **failed**.
    -   An improved error message if you create a username that includes non-supported characters.

Resolved in this release:

-   Impact analysis required reconfiguring after you upgraded Puppet Enterprise to version 2019.1.

Removed in this release:

-   **Chronological event view.** We've removed the option to view a chronological event history for your control repo or module.

## Version 2.12.2

Released 21 June 2019

Resolved in this release:

-   If you did not create any groups in your Continuous Delivery for PE user account before upgrading to version 2.12.0 or 2.12.1, you were unable to access the workspace that was automatically created for your user account as part of the upgrade.

## Version 2.12.1

Released 19 June 2019

Resolved in this release:

-   If you renamed a workspace and then navigated to a page in the **Settings** menu, you were redirected your default workspace.
-   User names were listed in the workspaces menu.
-   The link to a newly created workspace in the workspaces menu didn't work properly until the page was refreshed.

## Version 2.12.0

Released 17 June 2019

New in this release:

-   **Workspaces.** Teams using Continuous Delivery for PE need to share common resources, such as control repos, modules, pipelines, and jobs. Workspaces is a redesign of the users and team model in previous versions of Continuous Delivery for PE, and was created to make sharing resources with team members simpler and more intuitive. To learn more about how to set up and use workspaces, see [Managing workspaces](managing_workspaces.md#).

    **Note:** If you're upgrading to version 2.12.0 from an older version of Continuous Delivery for PE, your existing user accounts have been converted to workspaces.

-   **Redesigned navigation.** We've updated the navigation bar and moved it to the left side of your screen, bringing Continuous Delivery for PE into better visual and interface alignment with Puppet Enterprise.
-   **Global shared job hardware.** You can now set up global shared job hardware servers that are available to all workspaces in your Continuous Delivery for PE installation. Once global shared job hardware is configured, you can instruct a job to use shared hardware when creating or editing the job. To get started, see [Configure global shared job hardware](configure_job_hardware.md#).
-   **Link to the root console for super users.** Super users will now find a **Root Console** link in the workspaces menu at the top of the navigation bar.
-   **Add all members of an Active Directory or LDAP group to a workspace.** You can add existing LDAP or Active Directory groups to your installation's workspaces by clicking **Manage Groups** in the **Single Sign On** tab in the root console's Settings area.
-   **Support for unauthenticated SMTP.** When configuring SMTP, you are no longer required to enter a username and password.
-   **Select from available hardware capabilities.** When creating a new job, you're now shown a checklist list of available job hardware capabilities. This list is automatically populated by the capabilities you've set on your workspace's job hardware, as well as the capabilities on any available global shared job hardware.
-   **Usability improvements.** We've made numerous small usability improvements, including:
    -   In the **Edit Job** pane, pressing **Enter** no longer saves and exits an in-progress job.
    -   When you click the link to a commit SHA on a module's deployment details page, the link opens in a new browser tab.
    -   Deployments to protected environments now wait until all dependencies are complete before issuing an approval request.
    -   You'll see more helpful error messages when an impact analysis task fails.

Resolved in this release:

-   Prefixed environments failed to deploy successfully in certain circumstances.

-   The status of a commit in GitLab was not updated correctly once a pipeline stage completed.
-   In certain cases, impact analysis reports displayed an error when nodes in an environment had the potential to be impacted, but no impact was actually detected.
-   If you added new PE credentials and gave them the same name as existing credentials, the existing credentials were overwritten.
-   Users for whom Continuous Delivery for PE is not connected to the internet and analytics data collection is disabled received Google communication log warnings.

## Version 2.11.1

Released 10 June 2019

New in this release:

-   **Bitbucket Server webhooks created automatically.** Webhooks are now created automatically when you integrate Bitbucket Server \(5.x series and newer versions\) with Continuous Delivery for PE. You no longer need to manually configure plugins for webhooks or pull requests, as was required with the 4.x series.

Resolved in this release:

-   In some circumstances, PostgreSQL database users' jobs could not exit a pending state.


Removed in this release:

-   **Bitbucket Server 4.x series support for new users.** The Bitbucket Server 4.x series is no longer supported for new users who are setting up Continuous Delivery for PE for the first time.

## Version 2.11.0

Released 28 May 2019

New in this release:

-   **Deploy module code from a regex branch pipeline to a feature branch Puppet environment.** You can now deploy module code from a regex branch pipeline. When triggered, this deployment uses the Feature Branch deployment policy to deploy the new code to a Puppet environment with the same name as the branch where the new code is housed. If a Puppet environment by this name doesn't exist at the time of deployment, Continuous Delivery for PE creates it for you.


Resolved in this release:

-   You were unable to connect Continuous Delivery for PE to an LDAP server when using an insecure LDAP connection.


## Version 2.10.1

Released 20 May 2019

Resolved in this release:

-   Module deployment reports didn't work with disk storage. Disk storage users were not able to create or download module deployment reports.

-   Users could not render the upload-license page. This change fixes a previously unreported issue where a user could not visit the upload license page.


## Version 2.10.0

Released 13 May 2019

Important security notice:

-   **Select data was collected on accounts that have opted out of data collection.** We recently discovered that Continuous Delivery for PE was erroneously collecting select analytics data from accounts that had opted out of analytics data collection. We resolved this issue and have destroyed all analytics data collected on or before 9 May 2019, regardless of whether the data was collected as a result of this issue. To learn more about the data we collect, see [Analytics data collection](cd_analytics.md).


New in this release:

-   **Module deployment reports.** You can now generate a module deployment report that lists the most recent commit SHA in each environment in Continuous Delivery for PE for the 10 most recently deployed modules. The module deployment report is a comma-separated value \(CSV\) file, and can be downloaded and imported to the tool of your choice. To generate a module deployment report, click the reports icon ![](module-report.png) on theModules page.

-   **Usability improvements.** We've made numerous small usability improvements, including:

-   All forms can now be submitted by pressing the Enter key.

-   You can now show or hide the value of your Artifactory access token when adding credentials.

-   Success messages are no longer shown in red text in logs.

-   Improved error messaging when an invalid private key is added during impact analysis configuration.


Resolved in this release:

-   When you added a new control repo or module and were prompted to integrate your source control system, the newly added source control system was not selected correctly when you returned to the module or control repo creation screen.


Removed in this release:

-   **Job history.** We've removed the job history timeline from the Jobs page.


