---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Start building your module's pipeline

Once you set up a pipeline for a given branch of your module's control repo, every time you commit code to that branch, the jobs you've added to the pipeline will run automatically.

In this getting started guide, we'll set up a pipeline for your module repo. We'll add the job you created earlier to the pipeline, then use it to automatically test new code committed to your module.

## Stages and jobs in pipelines

Pipelines in Continuous Delivery for PE are made up of stages and jobs. Jobs are individual tests; stages group tests into a series of sequential phases.

A job, as we've seen, is a test for Puppet code. You can add a job or multiple jobs to your pipeline, and Continuous Delivery for PE will run them sequentially.

If you want to set up some logic of the "if this job succeeds, then run the next one, otherwise stop and tell me what happened," variety, use stages to break up the pipeline into a series of incremental steps. You can set your pipeline to require manual promotion before moving on to the next stage, or to promote automatically when certain conditions are met.

## Set up a pipeline

Set up a pipeline to enable automatic testing of newly added code by adding stages and jobs in the Continuous Delivery for PE web UI.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Modules**. Click the name of the module repo you wish to set up a pipeline for.

    Continuous Delivery for PE automatically creates a pipeline for the branch you selected when setting up the module repository \(this should be the master branch\). You'll see this branch name at the top of the Module Pipeline area of the web UI.

    **Tip:** You can also set up pipelines for other branches in this repo, in order to automatically run tests on the code changes that Continuous Delivery for PE makes on your behalf. To create a pipeline for a different branch, click **Add Pipeline**. You can undo this action with **Delete Pipeline** ![](delete_pipeline.png).

2.  Every pipeline needs at least one stage. Click **Add Stage**.

3.  Select a job from the list, and click **Add Stage**. Your job is added to the pipeline. Click **Done**.

    Your pipeline can be this simple, if you only need to run a single test on your new code. But if you wish to add additional tests, you can either add them to the existing stage by clicking Add Job, or create another stage.

4.  Create a second stage in your pipeline by clicking **Add Stage**. Select a job from the list \(for purposes of this getting started guide, it's fine to use the same job a second time\) and click **Add Stage**, then **Done**.

    CAUTION:

    Take care when constructing complex pipelines. If you wish to remove or reorder jobs in a multi-stage pipeline, you must delete the existing stages from the bottom of the pipeline up and recreate the pipeline with your changes. To delete all stages in a pipeline and start fresh, click **Delete Pipeline** ![](delete_pipeline.png).

5.  You now have a two-stage pipeline with a promotion step between the stages. Select **Auto Promote** and choose a promotion condition from the options.


### Result:

Your pipeline is ready for use. Next, you'll make a code change in your module repository and see the pipeline in action.

## Test code automatically with a pipeline

Every time you commit new code to your development or master branch, the module pipeline will run the jobs you've set up to test the module's code, and will report any errors or failures.

### About this task

### Procedure

1.  To see the pipeline in action, make a trivial change \(such as adding a new line to the README file\) in the master branch of your module repo. Commit your change.

2.  In the Continuous Delivery for PE web UI, click **Modules**, then click the name of the module repo you just committed code to.

3.  The module's Overview area now shows you the push event initiated by your code commit, and an entry for each job in your pipeline. The jobs report their status here, and will update to show their success or failure once the job run is complete.

    Each event summary includes a link to the commit in the control repo. Job events also include a job number; clicking on this number takes you to the Job Details page, where you can see the job's log for its run and review error messages.


### Result:

Now that we've seen how a pipeline runs when new code is committed, you'll set up a pull request gate in your pipeline. This allows you to run only a relevant part of the pipeline to test the code in a new pull request made against the pipeline branch.

## Test pull requests automatically

When "pull request" is enabled as a pipeline trigger, your pipeline will automatically test new code each time a pull request is opened against the relevant branch. By setting up a PR gate in the pipeline, you can ensure that the relevant tests run on the new code, but stop the pipeline before it proceeds to further tests or a deployment.

### About this task

**Note:** If you're a GitLab user, you might be more familiar with the term "merge request" than "pull request," which is used by GitHub and GitHub Enterprise. While the two terms can be used interchangeably, for the sake of simplicity and consistency, the Continuous Delivery for PE web UI and these instructions use the term "pull request" \(PR\).

### Procedure

1.  Add a pull request \(PR\) gate to your pipeline. A PR gate indicates that any pipeline runs triggered by a pull request should stop at a certain point in the pipeline. This allows you to automatically run acceptance tests on new pull requests, but to manage additional testing or deployment of the new code manually based on the results of the initial automated tests.

    1.  At the bottom of your pipeline's first stage, click **+ Add PR Gate**, and confirm your action.

    2.  When the gate has been added, you'll see a success message. Click **Done**.

        **Important:** You can only add one PR gate to a pipeline. If you decide to move the placement of a PR gate, delete the existing gate and create a new one in the desired stage.

2.  To see the PR gate in action, switch to a branch in your module repo other than master. Make a trivial change \(such as adding a new line to the README file\) in your module repo, and commit your change, then open a pull request against the master branch for this commit.

    **Remember:** The pull request must be made against the branch the pipeline is set up to use in order to trigger the pipeline.

3.  In the Continuous Delivery for PE web UI, click **Modules**, then click the name of the module repo you just committed code to.

4.  The module's Overview area now shows you the pull request event initiated by your code commit, and an entry for the job in your pipeline that precedes the PR gate. Note that the job in the pipeline's second stage, beyond the PR gate, has not been triggered.


### Result:

You now are familiar with the basics of testing new code with pipelines!

In the next getting started guide, you'll learn how to set up a control repo \(a process that will probably look quite familiar\) and perform your first code deployment. Click [Start deploying Puppet code](start_deploying.md#) when you're ready to begin.

