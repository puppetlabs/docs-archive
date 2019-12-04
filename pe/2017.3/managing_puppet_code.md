# Managing and deploying Puppet code

Puppet Enterprise \(PE\) includes built-in tools for managing and deploying your Puppet code. Code Manager and r10k can automatically install modules, create and maintain environments, and deploy new code to your masters, all based on code you keep in Git.

Code Manager automates the management and deployment of your new Puppet code. You push your code updates to your version control repository, and then r10k creates environments, installs modules, and deploys and syncs the new code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.

If you are already using r10k to manage your Puppet code, we suggest that you upgrade to Code Manager. Code Manager works in concert with r10k, so when you switch to Code Manager, you no longer interact directly with r10k.

If you're using r10k and aren't ready to switch to Code Manager yet, you can continue using r10k alone. You push your code changes to your version control repo, deploy environments from the command line, and r10k creates environments and installs the modules for each one.

Both Code Manager and the r10k code management tool are built into PE. To begin managing your code with either of these tools, you'll perform the following tasks:

1.  Create a control repository for maintaining your environments and code.

    Code management uses Git repository \(repo\) branches to create environments. Environments allow you to designate a node or node group to use a specific environment. For example, you could designate one node group to use the development environment and another to use the production environment. As you update the code in your control repo, code management tracks the state of that repo to keep each environment updated.

2.  Set up a Puppetfile to manage content in your environment.

    This file specifies which modules and data to install in your environment, including what version of that content should be installed, and where to download the content from.

3.  Configure Code Manager \(recommended\) or r10k.

    Configure code management in the console's master profile. If you need to customize your configuration further, you can do so by adding keys to Hiera.

4.  Deploy environments. With Code Manager, you can set up a webhook to deploy environments automatically. You can also trigger deployments manually from the command line. If you are using r10k alone, you'll run it manually from the command line whenever you want to deploy.

5.  Run Puppet to enforce code changes on your nodes. Whether you're changing just a few nodes for testing or on a larger set, use the orchestrator command line tool.


The following sections go into detail about setting up and using Code Manager and r10k.

-   **[Managing environments with a control repository](control_repo.md#)**  
To manage your Puppet code and data with either Code Manager or r10k, you need a control repository on Git. This control repository is where code management stores code and data to deploy your environments.
-   **[Managing environment content with a Puppetfile](puppetfile.md#)**  
A Puppetfile specifies detailed information about each environment's Puppet code and data, including where to get that code and data from, where to install it, and whether to update it.
-   **[Managing code with Code Manager](code_mgr.md)**  
Code Manager automates the management and deployment of your Puppet code. Push code updates to your source control repo, and then Puppet syncs the code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.
-   **[Managing code with r10k](r10k.md)**  
 r10k is a code management tool that allows you to manage your environment configurations \(such as production, testing, and development\) in a source control repository.
-   **[About file sync](filesync_about.md#)**  
File sync helps Code Manager keep your Puppet code synchronized across multiple masters.

