---
layout: default
title: "Setting up a control repository"
canonical: "/pe/latest/cmgmt_control_repo.html"
description: "Setting up a control repository for code management with Puppet Enterprise."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[r10k_config]: ./r10k_config.html
[r10k_custom]: ./r10k_custom.html
[r10k_run]: ./r10k_run.html
[r10k_ref]: ./r10k_ref.html
[upgrade]: ./code_mgr_upgrade.html
[filesync]: ./cmgmt_filesync.html
[control_repo_template]: https://github.com/puppetlabs/control-repo


[param_git]: ./r10k_custom.html#gitsettings
[direnv]: {{puppet}}/environments.html
[upgrade]: ./upgrade_mono.html
[environ_dir]: {{puppet}}/environments_configuring.html
[answer_file]: ./install_automated.html

Puppet Enterprise (PE) includes both Code Manager and r10k, so you don't need to separately download or install them. Before you begin using either of these code management tools, however, you need to make sure they can communicate with Git.

## Managing code with a control repository

Code management in PE uses Git repository (repo) branches to create [environments][direnv]. Environments allow you to designate a node or node group to use a specific environment. For example, you could designate one node group to use the development environment and another to use the production environment. As you update the code in your control repo, code management tracks the state of that repo to keep each environment updated.

Each branch of a connected repository is copied into a directory named after the branch. These environments are created in `/etc/puppetlabs/code/environments`on the Puppet master. For instance, if your repository is called "mycontrolrepo", with branches named "production", "test", and "development", your production branch becomes a production directory, the test branch becomes a test directory, and the development branch becomes a development directory. 

**Warning!** Because these environment directories are managed by Puppet's code management, **existing environments are not preserved**. Environments with the same name as the new one are overwritten. Environments not represented in the new control repo are erased. If you were using environments before, commit any necessary files or code to the appropriate new environment branch, or back them up somewhere *before* you start configuring code management.

## Set up your Git repo

Code management relies on version control, so before you begin configuring or using code management, set up your control repository:

1. [Create a Git repository](http://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository) to act as a repo for your code. You'll maintain this control repo using a development machine that is separate from your Puppet master.

2. Create a 'production' branch in your repo, and then delete the 'master' branch. The production branch is the default branch for your control repo. (Note: Your control repo **must** have a production branch.) If you are using multiple control repos, do not duplicate branch names. The Puppet [`control repo` template][control_repo_template] provides an example of a control repo.

  You'll base your other branches and environments on the production branch, but before you start creating those branches, you'll create a base production Puppetfile as detailed in your [next steps](#next-steps).

3. Generate a private SSH key to allow access to the control repository. See Github's [Generating SSH keys](https://help.github.com/articles/generating-ssh-keys/) for details on generating an SSH key for your repo. This SSH key cannot require a password. Your development machine needs full read/write access to your repos, and the Puppet master needs read access.

 4. **After** PE installation is complete, place this private key in a location that the `pe-puppet` user can access; we recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. Verify that the file itself has the correct permissions for the `pe-puppet` user, which is created during PE installation, to use that key.

## Next steps

Next, you'll set up a [Puppetfile][puppetfile] for managing your environment content with code management. If you already have a Puppetfile, you can now configure your code management.

Configuration steps differ, depending on whether you're using Code Manager (recommended) or r10k. For important information about the function and limitations of these tools, see the [Code Manager][code_mgr] page or the [r10k][r10k] page. For configuration instructions, see the [Code Manager configuration page][code_mgr_config] or the [r10k configuration page][r10k_config].
