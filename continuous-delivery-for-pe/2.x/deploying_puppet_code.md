---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Deploying Puppet code

-   **[Introducing deployments](introducing_deployments.md#)**  
Deployments in Continuous Delivery for PE use the PE tools you're familiar with--Code Manager, orchestration, and Puppet environments--to deploy your new code, but don't require you to use Git to move your commits between Puppet environments. Instead, Continuous Delivery for PE does that work for you.
-   **[Create environment node groups](cd_environment_node_groups.md)**  
In order for code deployments managed by Continuous Delivery for PE to work correctly, your environment node groups should be set up in a specific hierarchy.
-   **[Deployment policies](deployment_policies.md#)**  
Deployment policies are prescriptive workflows for Puppet code deployment that are built into Continuous Delivery for Puppet Enterprise \(PE\). You select the best deployment policy for your situation, and Continuous Delivery for PE does all the Git heavy lifting for you, deploying your code to the right nodes.
-   **[Deploy code manually](deploy_code_manually.md)**  
Use the manual deployment workflow to push a code change to a specified group of nodes on demand.
-   **[Deploy module code](deploy_module.md)**  
You can deploy new module code to your Puppet environments via a Continuous Delivery for PE module pipeline. To do so, you must first add a `:branch => :control_branch` declaration to the module's entry in your control repo's Puppetfile.
-   **[Require approval for deployments to protected Puppet environments](approval.md)**  
If your organization's business processes require manual review and approval before Puppet code is deployed to certain environments, set up an approval group of individuals with the authority to provide the needed review and sign-off. These approvers are contacted each time a deployment to a protected environment is proposed.

