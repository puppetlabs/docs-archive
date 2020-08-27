---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Deploy code manually

Use the manual deployment workflow to push a code change to a specified group of nodes on demand.

## About this task

## Procedure

1.  In the Continuous Delivery for PE web UI, click **Control Repos**, then select the control repo you wish to deploy from.

2.  Click **New Deployment** ![](deployment.png).

3.  Select the branch on which you made the change you're going to deploy \(master\), the commit you want to deploy, and your Puppet Enterprise instance.

4.  Select the Puppet environment you wish to deploy the change to.

5.  Select a deployment policy. For purposes of this getting started guide, select **Direct Deployment**.

    See [Deployment policies](deployment_policies.md#) to learn more about the deployment policies and how they work.

6.  Set termination conditions for this deployment, and choose the number of nodes that can fail before the deployment is stopped.

7.  Give the deployment a name, then click **Deploy**.


## Result:

Monitor the progress of your deployment on the deployment details page that opens when you launch the deployment.

