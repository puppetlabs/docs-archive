# Managing and deploying Puppet code

Puppet Enterprise \(PE\) includes built-in tools for managing and deploying your Puppet infrastructure. Code Manager and r10k are code management tools that automatically install modules, create and maintain environments, and deploy new code to your masters, all based on version control of your Puppet code and data.

Code management tools use Git version control to track, maintain, and deploy your Puppet modules, manifests, and data. This allows you to more easily maintain, update, review, and deploy Puppet code and data for multiple environments.

Code Manager automates the deployment of your Puppet code and data. You make code and data changes on your workstation, push changes to your Git repository, and then Code Manager creates environments, installs modules, and deploys the new code to your masters, without interrupting agent runs.

If you are already using r10k to manage your Puppet code, we suggest that you upgrade to Code Manager. Code Manager works in concert with r10k, so when you switch to Code Manager, you no longer interact directly with r10k.

If you're using r10k and aren't ready to switch to Code Manager yet, you can continue using r10k alone. You push your code changes to your version control repo, deploy environments from the command line, and r10k creates environments and installs the modules for each one.

Both Code Manager and the r10k code management tool are built into PE, so you don't have to install anything. To begin managing your code with either of these tools, you perform the following tasks:

1.  Create a control repository with Git for maintaining your environments and code.

    The control repository is the Git repository that code management uses to maintain and deploy your Puppet code and data and to create environments in your Puppet infrastructure. As you update your control repo, code management keeps each of your environments updated.

2.  Set up a Puppetfile to manage content in your environment.

    This file specifies which modules and data to install in your environment, including what version of that content to install, and where to download the content from.

3.  Configure Code Manager \(recommended\) or r10k.

    Configure code management in the console's master profile. If you need to customize your configuration further, you can do so by adding keys to Hiera.

4.  Deploy environments. With Code Manager, either set up a deployment trigger \(recommended\), or plan to trigger your deployment from the command line, which is useful for initial configuration. If you are using r10k alone, run it from the command line whenever you want to deploy.


The following sections go into detail about setting up and using Code Manager and r10k.

-   **[Managing environments with a control repository](control_repo.md#)**  
To manage your Puppet code and data with either Code Manager or r10k, you need a Git version control repository. This control repository is where code management stores code and data to deploy your environments.
-   **[Managing environment content with a Puppetfile](puppetfile.md#)**  
A Puppetfile specifies detailed information about each environment's Puppet code and data, including where to get that code and data from, where to install it, and whether to update it.
-   **[Managing code with Code Manager](code_mgr.md)**  
Code Manager automates the management and deployment of your Puppet code. Push code updates to your source control repo, and then Puppet syncs the code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.
-   **[Managing code with r10k](r10k.md)**  
 r10k is a code management tool that allows you to manage your environment configurations \(such as production, testing, and development\) in a source control repository.
-   **[About file sync](filesync_about.md#)**  
File sync helps Code Manager keep your Puppet code synchronized across multiple masters.

