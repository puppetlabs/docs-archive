---
layout: default
title: "Managing code with Code Manager"
canonical: "/pe/latest/code_mgr.html"
description: "About Code Manager, a system for managing and deploying Puppet code."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[filesync]: ./cmgmt_filesync.html
[cli]: ./code_mgr_cli.html


Code Manager automates the management and deployment of your Puppet code. You push code updates to your source control repo, and then Puppet syncs the code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.

# How Code Manager works

Under the hood, Code Manager uses r10k and the file sync service to stage, commit, and sync your code, automatically managing your directory environments and modules. 

First, you'll create a control repository with branches for each environment that you want to create (such as production, development, or testing). You'll also create Puppetfiles for your environments, specifying exactly which modules to install in each environment. This allows r10k to create directory environments, based on the branches you've set up.

When you push code to your control repo, you'll trigger Code Manager to pull that new code into a staging code directory (`/etc/puppetlabs/code-staging`). File sync then picks up those changes, pauses Puppet Server to avoid conflicts, and then syncs the new code to the live code directories on your Puppet masters.

Code Manager is built into Puppet Enterprise. Set up a default configuration of Code Manager, and then, if you require a more customized solution, you can customize your configuration by using Hiera.

## Understanding file sync and the staging directory

To sync your code across multiple masters and to make sure that code stays consistent, Code Manager relies on the Puppet file sync service. Without Code Manager or file sync, Puppet code lives in the codedir, or live code directory, at `/etc/puppetlabs/code`. But the file sync service looks for code in a code staging directory (`/etc/puppetlabs/code-staging`), and then syncs that to the live codedir. This means you can no longer write code to the codedir; instead, Code Manager moves new code from source control into the staging directory, and then file sync moves it into the live code directory.

> Don't directly modify code in the staging directory. Code Manager overwrites it with updates from the control repo. Similarly, don't modify code in the live code directory, because file sync overwrites that with code from the staging directory.

For more information specific to file sync, see [About file sync][filesync].

## Moving from r10k to Code Manager

Code Manager uses r10k in the background to improve automation of your code management and deployment. However, switching to Code Manager means you can no longer use r10k manually. Because of this, some r10k users might not want to move to Code Manager yet. Specifically, be aware of the following restrictions:

* If you use Code Manager, you **cannot** deploy code manually with r10k. If you depend on the ability to deploy modules directly from r10k, using the `r10k deploy module` command, you should continue to use your current r10k workflow.
* Code Manager must deploy all control repos to the same directory. If you are using r10k to deploy control repos to different directories, you should continue to use your current r10k workflow.
* Code Manager does not support system .SSH configuration or other shellgit options.
* Code Manager does not support post-deploy scripts.

If you rely on any of the above configurations, or any other r10k configuration that Code Manager doesn't yet support, you should continue to use your current [r10k][r10k] workflow. If you are using a custom script to deploy code, you should carefully assess whether Code Manager meets your needs.

## Getting started with Code Manager

To get Code Manager ready:

1. [Set up your control repository][repo].
2. [Create Puppetfiles for your environments][puppetfile].
3. [Configure Code Manager][code_mgr_config].
4. Optional: [Customize Code Manager's configuration][code_mgr_custom], if necessary.
5. Trigger Code Manager: 
  * from the [command line][cli]
  * with a [webhook][code_mgr_webhook]
  * with a cURL statement in a [custom script][scripts]
	
