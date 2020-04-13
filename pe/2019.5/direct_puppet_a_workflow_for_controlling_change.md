# Direct Puppet: a workflow for controlling change

The orchestrator—used alongside other PE tools, such as Code Manager—allows you to control when and how infrastructure changes are made before they reach your production environment.

The Direct Puppet workflow gives you precise control over rolling out changes, from updating data and classifying nodes, to deploying new Puppet code. In this workflow, you configure your agents to use cached catalogs during scheduled runs, and you send new catalogs only when you're ready, via orchestrator jobs. Scheduled runs continue to enforce the desired state of the last orchestration job until you send another new catalog.

**Related information**  


[Enable cached catalogs for use with the orchestrator \(optional\)](configuring_puppet_orchestrator.md#)

## Direct Puppet workflow

In this workflow, you set a up a node group for testing and validating code on a feature branch before you merge and promote it into your production environment.

### Before you begin

-   To use this workflow, you must enable cached catalogs for use with the orchestrator so that they enforce cached catalogs by default and compile new catalogs only when instructed to by orchestrator jobs.
-   This workflow also assumes you’re familiar with Code Manager. It involves making changes to your control repo—adding or updating modules, editing manifests, or changing your Hiera data. You’ll also run deploy actions from the Code Manager command line tool and the orchestrator, so ensure you have access to a host with PE client tools installed.

**Related information**  


[Installing PE client tools](installing_pe_client_tools.md#)

### Set up node groups for testing new features

The first step in the Direct Puppet workflow is to set up node groups for testing your new feature or code.

#### Procedure

1.  If they don't already exist, create environment node groups for branch testing, for example, you might create Development environment and Test environment node groups.

2.  Within each of these environment node groups, create a child node group to enable on-demand testing of changes deployed in Git feature branch Puppet environments.

    You now have at three levels of environment node groups: 1\) the top-level parent environment node group, 2\) node groups that represent your actual environments, and 3\) node groups specific to feature testing.

3.  In the **Rules** tab of the child node groups you created in the previous step, add this rule:

    |Option|Value|
    |------|-----|
    |Fact|`agent_specified_environment`|
    |Operator|`~`|
    |Value|`^.+`|

    This rule matches any nodes from the parent group that have the **agent\_specified\_environment** fact set. By matching nodes to this group, you give the nodes permission to override the server-specified environment and use their agent-specified environment instead.


#### Results

**Related information**  


[Create environment node groups](grouping_and_classifying_nodes.md#)

### Create a feature branch

After you've set up a node group, create a new branch of your control repository on which you can make changes to your feature code.

#### Procedure

1.  Branch your control repository, and name the new branch, for example, `my_feature_branch`.

    ![Diagram showing a production branch with an offshoot branch called my_feature_branch.](direct_puppet_create_branch.png)

2.  Make changes to the code on your feature branch. Commit and push the changes to the `my_feature_branch`.


### Deploy code to the Puppet master and test it

Now that you've made some changes to the code on your feature branch, you're ready to use Code Manager to push those to the Puppet master.

#### About this task

#### Procedure

1.  To deploy the feature branch to the master, run the following Code Manager command: `puppet code deploy --wait my_feature_branch`

    ![Diagram showing a production branch with an offshot branch called my_feature_branch, each containing code for a Puppet master.](direct_puppet_deploy_feature.png)

    **Note:** After this code deployment, there is a short delay while Puppet Server loads the new code.

2.  To test your changes, run Puppet on a few agent-specified development nodes in the **my\_feature\_branch** environment, run the following orchestrator command:

    ```
    puppet job run --nodes my-dev-node1,my-dev-node2 --environment my_feature_branch
    ```

    **Tip:** You can also use the console to create a job targeted at a list of nodes in the `**my\_feature\_branch**` environment.

    ![Diagram showing a production branch with an offshot branch called my_feature_branch, each containing code for a Puppet master. The production code is deployed to all production nodes as well as agent-specified nodes, while the feature branch code is deployed to a select group of agent-specified nodes, which are highlighted in the diagram.](direct_puppet_test_changes.png)

3.  Validate your testing changes. Open the links in the orchestrator command output, or use the Job ID linked on the Job list page, to review the node run reports in the console. Ensure the changes have the effect you intend.


**Related information**  


[Review jobs from the console](reviewing_jobs_in_the_console.md#)

[Run Puppet on a node list](running_puppet_on_demand_in_the_console.md#)

### Merge and promote your code

If everything works as expected on the development nodes, and you're ready to promote your changes into production.

#### About this task

#### Procedure

1.  Merge `my_feature_branch` into the `production` branch in your control repo.

    ![Diagram showing a production branch with an offshoot branch called my_feature_branch, which is then merged back into the production branch.](direct_puppet_promote.png)

2.  To deploy your updated `production` branch to the Puppet master, run the following Code Manager command: `puppet code deploy --wait production`


#### Results

### Preview the job

Before running Puppet across the `production` environment, preview the job with the `puppet job plan` command.

#### About this task

#### Procedure

1.  To ensure the job captures all the nodes in the `production` environment, as well as the agent-specified development nodes that just ran with the `my_feature_branch` environment, use the following query as the job target:

    ```
    puppet job plan --query 'inventory {environment in ["production", "my_feature_branch"]}
    ```


#### Results

### Run the job on the production environment

If you're satisfied with the changes in the preview, you're ready to enforce changes to the `production` environment.

#### About this task

#### Procedure

1.  Run the orchestrator job.

    ```
      puppet job run --query 'inventory {environment in ["production", "my_feature_branch"]}
    ```

    **Tip:** You can also use the console to create a job targeted at this PQL query.

    ![Diagram showing a production branch with an offshoot branch called my_feature_branch, which is then merged back into the production branch. From there, code is deployed to a Puppet master in the production environment, and then to production nodes. ](direct_puppet_run_production.png)


**Related information**  


[Run Puppet on a PQL query](running_puppet_on_demand_in_the_console.md#)

### Validate your production changes

Finally, you're ready to validate your production changes.

#### About this task

Check the node run reports in the console to confirm that the changes were applied as intended. If so, you're done!

Repeat this process as you develop and promote your code.

