---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Continuous Delivery for PE known issues

These are the known issues for the Continuous Delivery for PE 2.x release series.

## Module names are limited to 32 characters

If the name you give your module exceeds 32 characters, an `Invalid name. Valid names must only include printable ASCII characters` error is displayed.

## Automatic PE integration fails if the value of `puppetdb_port` is set as a string

If the `puppetdb_port` parameter's value in the `puppet_enterprise` class in the PE Infrastructure node group is set as a string, automatic integration of PE fails with an `Automatic configuration failed` error. To work around this issue, use the PE console to change the `puppetdb_port` parameter's value to an integer.

## Regex branch module deployments fail if the `:control_branch` pattern is used for multiple modules

Deploying a module from a regex branch pipeline fails if more than one module in your Puppetfile uses the `:branch => :control_branch` pattern. To work around this issue, make sure that the `default_branch` parameter is set in the Puppetfile for every Git-sourced module that uses the `:branch => :control_branch` pattern.

## Docker configuration changes to jobs are not immediately available

When you update the Docker configuration for a job, several minutes must elapse for your changes to take effect. To work around this issue, wait at least five minutes after making a Docker configuration change before attempting to run the job.

## Add new workspace screen inaccessible to users removed from all workspaces

If you delete or are removed from all workspaces of which you are a member, you are directed to the Add New Workspace screen. If you log out or navigate away from this screen without creating a new workspace, you are unable to access any workspaces or get back to the Add New Workspace screen until invited to an existing workspace by another user. To work around this issue, create a new workspace when prompted, or request an invitation to an existing workspace.

