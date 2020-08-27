---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Deploy module code

You can deploy new module code to your Puppet environments via a Continuous Delivery for PE module pipeline. To do so, you must first add a `:branch => :control_branch` declaration to the module's entry in your control repo's Puppetfile.

## About this task

Module code is deployed to your Puppet environments using the eventual consistency deployment policy. When you trigger a module deployment, Continuous Delivery for PE creates a new branch in your module repository with the same name as your target Puppet environment. This new branch contains the code to be deployed.

Continuous Delivery for PE then triggers Code Manager, which reads the `:branch => :control_branch` declaration referring to the module in the control repo's Puppetfile and adds the new module code to the control repo. The new module code is now ready to be deployed to your chosen Puppet environments on the next scheduled Puppet run.

## Procedure

1.  Open the Puppetfile that includes the module you wish to deploy. Add a `:branch => :control_branch` declaration to the module's section of the Puppetfile, as in the following example.

    ```
    mod 'apache',
      :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
      :branch => :control_branch.
      :default_branch => 'master'
    ```

    To learn more about the `:branch => :control_branch` option, see [Declare content from a relative control repo branch](https://puppet.com/docs/pe/latest/puppetfile.html#declare-content-from-a-relative-control-repo-branch) in the Code Manager documentation.

2.  In the Continuous Delivery for PE web UI, click **Modules** and select the module you wish to deploy.

3.  If you haven't already done so, create a pipeline for your module.

4.  Click **+ Add Stage**. Select **Module Deployment**.

5.  Select your PE instance and choose the Puppet environment the module code will be deployed to.

6.  Click **Add Deployment**.


## Result:

Your module pipeline is now set up to deploy new module code to your chosen Puppet environments any time the pipeline is triggered.

