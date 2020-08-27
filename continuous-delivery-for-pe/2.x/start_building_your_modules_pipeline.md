---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Constructing pipelines

Once you set up a pipeline for a given branch of your control repo or module, every time you commit code to that branch, the tasks you've added to the pipeline run automatically.

## Stages and tasks in pipelines

Pipelines in Continuous Delivery for PE are made up of stages and tasks. Tasks include jobs to test code, deployments, and impact analysis; stages group tasks into a series of sequential phases.

If you want to set up some logic of the "if this job succeeds, then run the next one, otherwise stop and tell me what happened," variety, use stages to break up the pipeline into a series of incremental steps. You can set your pipeline to require manual promotion before moving on to the next stage, or to promote automatically when certain conditions are met.

## Set up a pipeline

Set up a pipeline to enable automatic testing of newly added code by adding stages and jobs in the Continuous Delivery for PE web UI.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Control Repos**. Click the name of the control repo you wish to set up a pipeline for.

    Continuous Delivery for PE automatically creates a pipeline for the branch you selected when setting up the control repo \(the master branch\). You'll see this branch name at the top of the Control Repo Pipeline area of the web UI.

    **Tip:** You can also set up pipelines for other branches in this repo, in order to automatically run tests on the code changes that Continuous Delivery for PE makes on your behalf. To create a pipeline for a different branch, click **Add Pipeline**. You can undo this action with **Delete Pipeline** ![](delete_pipeline.png).

2.  Every pipeline needs at least one stage. Click **+ Add Stage** and select **Job**.

3.  Select a job from the list, and click **Add Stage**. Your job is added to the pipeline. Click **Done**.

    If you need to run a single test on your new code, your pipeline can be this simple. But if you wish to add additional tests, you can either add them to the existing stage by clicking **Add Job**, or create another stage.

4.  Create a second stage in your pipeline by clicking **Add another stage** and selecting **Job**. Select a job from the list and click **Add Stage**, then **Done**.

    **Tip:** If you wish to remove or reorder the stages in your pipeline, click **More Actions** ![](kebab.png). To delete all stages in a pipeline and start fresh, click **Delete Pipeline** ![](delete_pipeline.png).

5.  You now have a two-stage pipeline with a promotion step between the stages. Select **Auto Promote** and choose a promotion condition from the options.


### Result:

Your pipeline is ready for use.

## Test code automatically with a pipeline

Every time you commit new code to your development or master branch, the pipeline runs the jobs you've set up to test the code and reports any errors or failures.

### About this task

### Procedure

1.  To see the pipeline in action, make a trivial change \(such as adding a new line to the README file\) in the master branch of your control repo. Commit your change.

2.  In the Continuous Delivery for PE web UI, click **Control Repos**, then click the name of the control repo you just committed code to.

3.  The Overview area now shows you the push event initiated by your code commit, and an entry for each job in your pipeline. The jobs report their status here, and update to show their success or failure when the job run is complete.

    Each event summary includes a link to the commit in the control repo. Job events also include a job number; clicking on this number takes you to the Job Details page, where you can see the job's log for its run and review error messages.


### Result:

## Test pull requests automatically

When "pull request" is enabled as a pipeline trigger, your pipeline automatically tests new code each time a pull request is opened against the relevant branch. By setting up a PR gate in the pipeline, you can ensure that the relevant tests run on the new code, but stop the pipeline before it proceeds to further tests or a deployment.

### About this task

**Note:** If you're a GitLab user, you might be more familiar with the term "merge request" than "pull request," which is used by GitHub and GitHub Enterprise. Although the two terms can be used interchangeably, for the sake of simplicity and consistency, the Continuous Delivery for PE web UI and these instructions use the term "pull request" \(PR\).

### Procedure

1.  Add a pull request \(PR\) gate to your pipeline. A PR gate indicates that any pipeline runs triggered by a pull request stops at a certain point in the pipeline. This allows you to automatically run acceptance tests on new pull requests, but to manage additional testing or deployment of the new code manually based on the results of the initial automated tests.

    1.  In your pipeline's first stage, click **More Actions** ![](kebab.png). Click **Add Item to Stage**, and select **Pull Request Gate**.

    2.  Click **Add PR Gate**. When the gate has been added, you'll see a success message. Click **Done**.

        **Important:** You can add only one PR gate to a pipeline. If you decide to move the placement of a PR gate, delete the existing gate and create a new one in the desired stage.

2.  To see the PR gate in action, switch to a branch in your control repo other than master. Make a trivial change \(such as adding a new line to the README file\) in your control repo, and commit your change, then open a pull request against the master branch for this commit.

    **Remember:** The pull request must be made against the branch the pipeline is set up to use in order to trigger the pipeline.

3.  In the Continuous Delivery for PE web UI, click **Control Repos**, then click the name of the control repo you just committed code to.

4.  The Overview area now shows you the pull request event initiated by your code commit, and an entry for the job in your pipeline that precedes the PR gate. Note that the job in the pipeline's second stage, beyond the PR gate, has not been triggered.


### Result:

## Deploy code automatically with a pipeline

You can use a control repo pipeline to automatically deploy new code to a specified set of nodes every time a commit is made.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Control Repos**. Click the name of the control repo you've created a pipeline for.

2.  Click **+ Add Stage**.

3.  In the Add new Stage window, select **Deployment**.

4.  Select your Puppet Enterprise instance and the node group you wish to deploy changes to every time this pipeline runs.

5.  Select a deployment policy. For purposes of this getting started guide, select **Direct Deployment**.

    See [Deployment policies](deployment_policies.md#) to learn more about the four deployment policies and how they work.

6.  Set termination conditions for this pipeline's deployments, and choose the number of nodes that can fail before the deployment is stopped.

7.  Click **Add Stage** and close the Add new Stage window. Your new stage, showing the details of the deployment, is added to the pipeline.

8.  To see the pipeline in action, make a trivial change \(such as adding a new line to the README file\) on the master branch of your control repo. Commit your change.

    The control repo Overview area now shows you the push event initiated by your code commit, and a deployment event. The deployment reports its status here, and updates to show its success or failure when the deployment is complete.

    Each event summary includes a link to the commit in the control repo. Deployment events also include a deployment number; clicking on this number takes you to the Deployment Details page, where you can see more information.

9.  You can run the same deployment again from the web UI by retriggering the webhook. In the Overview area, locate the push event you wish to rerun, and click **View Webhook** ![](webhook.png).

    The webhook's request and response data is shown in the Webhook Data pane, which can be useful for troubleshooting.

10. Click **Redeliver Webhook**, and confirm your action. The Overview area now shows the new event triggered by the redelivery of the webhook.

    **Note:** If you've made changes to your pipeline since the last time this webhook was delivered, the redelivered webhook follows the current pipeline's sequence and rules.


### Result:

