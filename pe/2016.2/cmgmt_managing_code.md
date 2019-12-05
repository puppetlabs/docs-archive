---
layout: default
title: "Managing and deploying Puppet code"
canonical: "/pe/latest/cmgmt_managing_code.html"
description: "Managing and deploying Puppet code in Puppet Enterprise."
---

[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[control_repo]: ./cmgmt_control_repo.html
[direnv]: {{puppet}}/environments.html

Puppet Enterprise includes built-in tools, Code Manager and r10k, for managing and deploying your Puppet code. These tools can automatically install Puppet modules, create and maintain [environments][direnv], and deploy new code to your masters, all based on code you keep in Git.

Code Manager automates the management and deployment of your new Puppet code. You push your code updates to your Git repository, and then Puppet creates environments, installs modules, and deploys and syncs the new code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs. 

If you are already using r10k to manage your Puppet code, we suggest that you upgrade to Code Manager. Code Manager works in concert with r10k, so when you switch to Code Manager, you no longer interact directly with r10k.

If you're using r10k and aren't ready to switch to Code Manager yet, you can continue using r10k alone. You push your code changes to your source control repo, run r10k on the command line, and r10k creates environments and installs the modules for each one.

Both the Code Manager system and the r10k code management tool are built into PE, so you don't have to install anything. To get started:

1. Create a [control repository][control_repo] for maintaining your environments and code.
  
    Code management in PE uses your existing Git repository (repo) branches to create [environments][direnv]. Environments allow you to designate a node or node group to use a specific environment. For example, you could designate one node group to use the development environment and another to use the production environment. As you update the code in your control repo, code management tracks the state of that repo to keep each environment updated.
  
2. Set up [Puppetfiles][puppetfile], if you want to install modules in your environments.

    Puppetfiles specify which modules to install in your environments, including what version of those modules should be installed, and where Puppet should get them.

3. Configure Code Manager (recommended) or r10k.
  
    Configure Code Manager or r10k in the console's master profile. If you need to customize your configuration further, you can do so by adding keys to Hiera. See the [Code Manager][code_mgr] or [r10k][r10k] pages for configuration instructions.

4. Set up a deployment trigger to run Code Manager automatically. You can also trigger Code Manager manually from the command line. If you are using r10k alone, you'll continue to run it manually from the command line whenever you want to deploy. See the [Code Manager][code_mgr] or [r10k][r10k] pages for information about triggering Code Manager or running r10k.
