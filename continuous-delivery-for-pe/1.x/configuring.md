---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configuring and adding integrations

Configure your Continuous Delivery for Puppet Enterprise \(PE\) instance so that it communicates with your source control system, Puppet Enterprise, and the job hardware you will use to run tests on your Puppet code.

-   **[Integrate with source control](integrations.md#)**  
Integrate your source control system with Continuous Delivery for Puppet Enterprise \(PE\) by following the appropriate set of instructions on this page.
-   **[Integrate with Puppet Enterprise](integrate_with_puppet_enterprise.md#)**  
To set up an integration between your Puppet Enterprise \(PE\) instance and Continuous Delivery for PE, you must first set up a dedicated PE user with appropriate permissions, then add your PE instance's credentials to Continuous Delivery for PE.
-   **[Configure job hardware](configure_job_hardware.md#)**  
Job hardware, or the servers Continuous Delivery for Puppet Enterprise \(PE\) uses to run tests on your Puppet code, must be configured before you can begin running tests or using pipelines. 
-   **[Configure Bitbucket Server repositories](configure_bitbucket.md#)**  
In order to make full use of the core features of Continuous Delivery for Puppet Enterprise \(PE\), Bitbucket Server users must enable two plugins. These plugins enable the webhooks and pull request notifications Continuous Delivery for PE uses to automate testing and deployment of your code.

