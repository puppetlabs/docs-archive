---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Getting started with Continuous Delivery for PE

In this getting started guide, you'll learn how to use the features of Continuous Delivery for Puppet Enterprise \(PE\) to test code, deploy code changes, and build automated testing and deployment pipelines. 

For best results, work through the getting started guide in the order presented here.

-   **[Start setting up your workspace](setting_up.md#)**  
As a new user, you'll need to perform some initial workspace setup tasks before you can begin using the core features of Continuous Delivery for PE. This guide will also provide our recommendations for how to think about and work with environments and Git branches as you use Continuous Delivery for PE.
-   **[Start testing module code with jobs](start_testing_module_code.md#)**  
Jobs are customizable tests for your Puppet code. In this section of the getting started guide, we'll show you how to write a new job, install dependencies on your job hardware, and dispatch your job on demand.
-   **[Start building your module's pipeline](start_building_your_modules_pipeline.md#)**  
Once you set up a pipeline for a given branch of your module's control repo, every time you commit code to that branch, the jobs you've added to the pipeline will run automatically.
-   **[Start deploying Puppet code](start_deploying.md#)**  
In this getting started guide, you'll learn how to set up a control repo, manually kick off a deployment, create a deployment pipeline, and gain insight into how Continuous Delivery for PE works behind the scenes to deploy code changes to your nodes.

