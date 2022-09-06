# Managing code with Code Manager

Code Manager automates the management and deployment of your Puppet code. Push code updates to your source control repo, and then Puppet syncs the code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.

-   **[How Code Manager works](code_mgr_how_it_works.md#)**  
 Code Manager uses r10k and the file sync service to stage, commit, and sync your code, automatically managing your environments and modules.
-   **[Configuring Code Manager](code_mgr_config.md#)**  
To configure Code Manager, you'll enable it in Puppet Enterprise \(PE\), set up authentication, and test the communication between the control repo and Code Manager.
-   **[Customize Code Manager configuration in Hiera](code_mgr_customizing.md#)**  
To customize your Code Manager configuration, set parameters with Hiera.
-   **[Triggering Code Manager on the command line](puppet_code.md#)**  
 The `puppet-code` command allows you to trigger Code Manager from the command line to deploy your environments.
-   **[Triggering Code Manager with a webhook](code_mgr_webhook.md#)**  
To deploy your code, you can trigger Code Manager by hitting a web endpoint, either through a webhook or a custom script. The webhook is the simplest way to trigger Code Manager.
-   **[Triggering Code Manager with custom scripts](code_mgr_scripts.md#)**  
Custom scripts are a good way to trigger deployments if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications.
-   **[Troubleshooting Code Manager](code_mgr_troubleshoot.md#)**  
Code Manager requires coordination between multiple components, including r10k and the file sync service. If you have issues with Code Manager, check that these components are functioning.
-   **[Code Manager API](code_manager_api.md#)**  
Use Code Manager endpoints to deploy code and query environment deployment status on your masters without direct shell access.

