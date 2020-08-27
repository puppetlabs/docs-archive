---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Getting started with Continuous Delivery for PE

Greetings! Welcome to Continuous Delivery for PE. If you're trying out the software for the first time, this getting started guide is for you. As a new user, you'll need to perform some initial workspace setup tasks, and then we'll show you how to begin using core features of Continuous Delivery for PE.

You're just a few steps away from a more streamlined, powerful, and flexible Puppet code delivery process. Ready to get started?

## Step 1: Install Continuous Delivery for PE

We'll start by installing Continuous Delivery for PE directly from the PE console.

### About this task

These installation instructions assume you are running PE version 2019.1.x or 2019.2.x on an Enterprise Linux 7 node. If this is not the case, see [Which installation method should I choose?](install_set_up.md) and follow the instructions for the installation method that suits your infrastructure.

### Procedure

1.  In the PE console, click **Integrations**.

2.  In the **Continuous Delivery for PE host** field, enter the name of the node where you wish to install Continuous Delivery for PE.

    **Note:** This node must be running Enterprise Linux 7, and must have Puppet installed.

3.  In the **Administrator email** field, enter an email address for the Continuous Delivery for PE administrator, also known as the root user. This email address is used as your username when signing in to Continuous Delivery for PE as the root user.

4.  In the **Administrator password** field, enter a strong password for the Continuous Delivery for PE administrator account.

5.  Click **Install** to perform a default installation of Continuous Delivery for PE.

    **Important:** Your Puppet certificate name must be a resolvable DNS hostname.

6.  When the job is complete, navigate to the URL printed on the task page. Click **Trial Mode** to start a free seven-day trial.


## Step 2: Create your user account and set up a workspace

Think of a workspace like a neighborhood within the Continuous Delivery for PE city. Your workspace is where you store and access resources such as control repos, pipelines, and jobs. When you're ready to collaborate, you can invite the members of your team to join your workspace.

### About this task

### Procedure

1.  If you haven't already done so, navigate to the Continuous Delivery for PE web UI address you received at the end of the installation process in Step 1.

2.  Create your user account.

    1.  On the login page, click **Create an account**.

    2.  Fill in the registration form and create a username and password.

    3.  Click **Sign Up**.

3.  Set up a workspace.

    1.  On the Choose a workspace screen, click **+ Add New Workspace**.

    2.  Enter a name for your workspace and click **Create workspace**.


## Step 3: Set up integrations and configure job hardware

Next, it's time to set up integrations with your PE instance and the source control system where you keep your Puppet code. You'll also configure a job hardware server, where your code will be tested before deployments.

We're sending you to our integration docs to complete these tasks.

1.  Follow our [Integrate with Puppet Enterprise](integrate_with_puppet_enterprise.md#) instructions.

    **Note:** This guide assumes you're integrating a PE version 2019.1.x or 2019.2.x instance, which automatically configures impact analysis for you. If you're integrating a different version of PE, follow the [Configure impact analysis](configure_impact_analysis.md#) instructions once your integration is complete.

2.  Select your source control system from the list below and follow the integration instructions:
    -   [Azure DevOps](integrations.md#)
    -   [Bitbucket Cloud](integrations.md#)
    -   [Bitbucket Server](integrations.md#)
    -   [GitHub](integrations.md#)
    -   [GitHub Enterprise](integrations.md#)
    -   [GitLab](integrations.md#)
3.  Follow our [Configure job hardware with the web UI](configure_job_hardware.md#) instructions to set up a job hardware server.

## Step 4: Add a control repo

A control repo in Continuous Delivery for PE tracks the changes made on the active development branch of your source control system. When adding a control repo to Continuous Delivery for PE, it's important to connect the master Git branch.

### About this task

When you set up your new control repo, Continuous Delivery for PE adds a webhook to the associated repository in your source control system. The webhook reports new commit activity on the repository to Continuous Delivery for PE, enabling you to track code changes and take action.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Control Repos**, then click **Add Control Repo**.

2.  Follow the prompts to select your source control, organization, and your chosen repository.

3.  The master branch of your repository is automatically selected for you. If your control repo does not currently contain a master branch, follow the prompts to let Continuous Delivery for PE create one for you, and select the branch that's under active development to create the master branch from.

    **Important:** When working with Continuous Delivery for PE, commit only to the master branch and to any feature branches \(which are eventually merged back into the master branch\). Do not push code changes to any of your other Git branches, as doing so can create conflicts with Continuous Delivery for PE workflows.

4.  Edit the name of your new control repo.

    **Tip:** The control repo name must contain only alphanumeric characters, dashes, and underscores.

5.  Click **Add**. The control repo is now shown in the master list on the Control repos page.


## Step 5: Set up a pipeline

### About this task

Pipelines in Continuous Delivery for PE are made up of stages and tasks. Tasks include jobs to test code, deployments, and impact analysis; stages group tasks into a series of sequential phases.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Control Repos**. Click the name of the control repo you added in Step 4.

2.  On the right side of the web UI, you'll see the space where we'll create your pipeline. Click **+ Add default pipeline**.

3.  Your default pipeline is automatically created for your master branch. This pipeline contains three stages:

    -   The **Code Validation stage** includes two jobs \(tests for Puppet code\).
    -   The **Impact Analysis stage** includes an impact analysis task with a pull request gate \(more on this later\).
    -   The **Deployment stage**, where we'll add deployment instructions in step 7.

## Step 6: Set up an environment node group

To give us a place to demonstrate how Continuous Delivery for PE deploys new code to your Puppet Enterprise- managed nodes, we'll next set up a small environment node group to use for deployment testing.

### About this task

### Procedure

1.  In your Git repository, create a new branch called **cd4pe-testing**. This will represent the environment node group.

2.  On your master, run `puppet code deploy cd4pe-testing`.

3.  In the PE console, click **Classification**.

4.  Click **Add group...** and create a new node group with the following specifications:

    -   Parent name: All Environments

    -   Group name: CD4PE test group

    -   Environment: cd4pe\_testing

    -   Environment group: yes

    -   Description: Node group used for Continuous Delivery for PE testing

    Click **Add**.

5.  In the list of node groups, click **CDPE test group**. In the **Rules** tab, pin two or three test nodes to the node group. Make sure these nodes are not assigned to any other node groups in your PE installation.


## Step 7: Deploy changes to your nodes

Once you've added a deployment to your pipeline, you can automatically move code changes to your nodes following the deployment conditions you've set.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, in your control repo's pipeline, click **Add a deployment**.

2.  Select your PE instance, then select the **CDPE test group** node group we created in Step 5.

3.  Select the **Direct Deployment Policy**. Don't worry about setting special conditions for this deployment.

4.  Click **Add Deployment to Stage**. On the confirmation screen, click **Done**.

5.  Now we need a change to pass through to production. In your Git repository, on the master branch, make a code change such as updating a package version. Commit the change and then return to the Continuous Delivery for PE web UI to watch your pipeline in action.


### Result:

When triggered by your commit, the pipeline automatically runs the two tests on your code, skips over the impact analysis stage that we haven't yet set up, and stops, as the pipeline is set up not to autopromote into the Deployment stage. Click **Promote** to continue the pipeline run and launch the deployment. The results of the pipeline run are logged in the Events area on the left of the screen.

## Step 8: Create an impact analysis report

An impact analysis report shows the potential impact that new Puppet code will have on the nodes and resources you're managing with PE. You can add impact analysis tasks to your pipeline, and you can generate impact analysis reports on demand, as we'll do in this step.

### About this task

### Procedure

1.  First, create a change for the report to analyze. We'll generate an impact analysis report to review how this change impacts the nodes in your CDPE test group. In your Git repository, create a feature branch from your master branch

2.  On the feature branch, make a code change, such as updating a package version.

3.  Commit the change on the feature branch and then return to the Continuous Delivery for PE web UI.

4.  In the Continuous Delivery for PE web UI, click ![](impact-analysis.png) **New Impact Analysis**.

5.  In the New Impact Analysis window, select your feature branch, then select the commit you just made. Select your PE instance and the CDPE test group node group.

6.  Click **Analyze** to generate the report.

    Continuous Delivery for PE will now generate a new catalog containing your commit, and will compare this new catalog to the current catalog of the nodes in the CDPE test group.

7.  Click **View Impact Analysis**. The report shows how the change on your feature branch would impact your nodes and resources if it was merged to the master branch.


### Result:

Congratulations! You've reached the end of this introductory guide. You're now familiar with some of the core features of Continuous Delivery for PE, and have a basic understanding of how the software helps you deploy Puppet code and preview the impact of changes.

