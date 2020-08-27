---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Release notes

New features, enhancements, known issues, and resolved issues for the Continuous Delivery for Puppet Enterprise 1.x release series.

## Version 1.4.1

Released 7 August 2018

Resolved in this release:

-   **Webhook errors in version 1.4.0 caused by a missing table. **A missing binding caused a table generation error, which in turn impacted the firing of webhooks.


## Version 1.4.0

Released 7 August 2018

New in this release:

-   **Bitbucket Server support.** See [Integrate with Bitbucket Server](integrations.md#) and [Configure Bitbucket Server repositories](configure_bitbucket.md#) for instructions on how to set up your integration, configure two required plugins, and start using your organization's Bitbucket Server repositories with Continuous Delivery for PE.

-   **More intuitive color-coding on the control repo and module overview pages.** Control repos and modules now display a red status icon if there were any failures during the most recently executed job and deployment. A green status icon is shown only if all jobs and deployments were successful.

-   **Pull requests are automatically set as a pipeline trigger when a PR gate is added to a pipeline.**


## Version 1.3.2

Released 27 July 2018

Resolved in this release:

-   **Amazon DynamoDB users could not successfully integrate with GitHub.** Users who set up Amazon DynamoDB as a Continuous Delivery for PE database and then attempted to integrate with GitHub could not complete the integration.


## Version 1.3.1

Released 25 July 2018

Resolved in this release:

-   **Incorrect commit used by the pipeline when merging a GitLab pull request with multiple commits.** When a GitLab pull request with multiple commits was merged, the pipeline used the first commit in the pull request as the push event, not the most recent commit to the branch.


## Version 1.3.0

Released 19 July 2018

New in this release:

-   ****View the results of jobs and deployments directly in source control next to the commits or pull requests that triggered them.****When a job or deployment runs as part of a pipeline, the results are automatically posted to the relevant commit in GitHub, GitHub Enterprise, or GitLab.

-   **Manually trigger a pipeline.** Click **Trigger Pipeline** to kick off a pipeline run without making a new commit or pull request.

-   **Stop an in-progress deployment.** To stop a deployment once it is underway, click **Cancel** on the deployment's details page.

-   **Delete a pipeline.**If you've created multiple pipelines for a single module or control repo, click **Delete Pipeline** ![](delete_pipeline.png) to remove one. If your module or control repo has just one pipeline, clicking **Delete Pipeline** removes all the stages in the pipeline so you can build it from scratch.


Resolved in this release:

-   **Puppet Enterprise integrations missing after editing session.** Editing the details of a Puppet Enterprise integration caused other Puppet Enterprise integrations to vanish from the list of available credentials.


## Version 1.2.1

Released 6 July 2018

New in this release:

-   **Manually promote a pipeline to the next stage.** If your pipeline does not have auto-promotion enabled, or a pipeline run does not meet the auto-promotion conditions you've set, click **Promote** ![](promote.png) to trigger the pipeline's next stage.


Resolved in this release:

-   **A new license file could not be successfully uploaded before the previous license file expired.**

-   **Pre-deployment checks and improved error messaging for deployments that would delete the default GitLab branch.**


## Version 1.2.0

Released 5 July 2018

New in this release:

-   **Pull request pipeline trigger.**Pull \(or merge\) requests submitted against a branch for which you've set up a pipeline now trigger the pipeline. See [Test pull requests automatically](start_building_your_modules_pipeline.md#) for more information.

-   **Pull request gates for pipelines.** Add a pull request gate to your pipeline to indicate that a pipeline triggered by a pull request should stop at that point in the pipeline.

-   **Manually trigger a pipeline by redelivering a webhook.** Run a pipeline from the web UI by redelivering the webhook associated with any push or pull request event.

-   **Manage your** **SSH key.**Go to **Settings** \> **SSH Key** to create, view, or delete the SSH key associated with your account.

-   **Automatic browser cache refresh on update.**When you upgrade to a new version of Continuous Delivery for PE, your browser cache is automatically refreshed so that new web UI features are displayed immediately.


Resolved in this release:

-   **GitHub integration didn't check for OAuth application.** Users attempting to integrate with GitHub were shown an **Add Credentials** button before a GitHub OAuth application was established.

-   **Blue and green branches failed to alternate.** When included in a control repo pipeline, deployments using the blue-green branch policy did not alternate between the blue and green branches as expected.


## Version 1.1.0

Released 21 June 2018

New in this release:

-   **GitHub support.** See [Integrate with GitHub](integrations.md#) for instructions on how to create an OAuth application to start using your organization's GitHub repositories with Continuous Delivery for PE.

-   **Profile page.** Click **View Profile** in the user menu ![](user_icon.png) to see your username and account ID number, or to update the email address associated with your account.

-   **Pre-deployment check for protected branches in GitLab repositories.**If you attempt to launch a deployment on a protected branch in your GitLab-hosted repository, Continuous Delivery will report an error and cancel the deployment.


Resolved in this release:

-   **In a control repo pipeline, job stages were not triggered by the completion of deployment stages.**If your control repo pipeline included a deployment stage that automatically promoted to a job stage, the intended dependency chain was not not followed.

-   **Storage settings values trimmed.**This prevents errors due to the accidental inclusion of white space.

-   **Improved error messages.**Users who have not configured SMTP will see a more helpful error message when attempting operations that require email.

-   **Unnecessary page refreshes on the SMTP page.**Toggling the TLS switch on the SMTP page in the root console no longer refreshes the page unnecessarily.


## Version 1.0.2

Released 13 June 2018

New in this release:

-   **Create and manage Continuous Delivery agent credentials.** By adding your agent credentials to the `distelli.yml` file, you can provision new job hardware with the agent automatically connected to your account instead of entering your credentials manually on each new server. See [Configure job hardware with `distelli.yml`](configure_job_hardware.md#) for more information.


Resolved in this release:

-   **Redirect loops.**Expired Continuous Delivery for PE licenses created redirect loops, as did closing the browser tab during the guided installation process and returning to web UI in a new browser tab.

-   **Broken link to the sample jobs documentation.**


## Version 1.0.1

Released 5 June 2018

New in this release:

-   **Automatically detected job hardware capabilities shown in the web UI.**The Continuous Delivery agent automatically detects job hardware capabilities such as "Linux" or "Windows." These agent-generated capabilities are now displayed on the Job Hardware page.

-   **Pull request pipeline trigger removed.** We are working on an improved workflow for triggering testing and deployments with a pull request, and will reintroduce this feature in a future release.

-   **More accurate labeling on the deployment details page.**


Resolved in this release:

-   **Wrong source branch used for jobs triggered by pull requests.** Jobs triggered by pull requests were attempting to clone the target branch of the pull request, leading to a job failure.

-   **Missing code deploy at the end of the temporary branch deployment policy's workflow.**The selected commit was not being applied to the target Puppet environment, so the next scheduled Puppet run would overwrite any changes made during the deployment.

-   **Timestamps displaying AM and PM incorrectly.**


## Version 1.0.0

Released 1 June 2018

Hello, world! This is the initial release of Continuous Delivery for Puppet Enterprise.

