---
layout: default
title: "Managing environments with a control repository"
canonical: "/pe/latest/cmgmt_control_repo.html"
description: "Managing environments with a control repository for code management with Puppet Enterprise."
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
[code_mgr_cli]: ./code_mgr_cli.html
[r10k_deploy]: ./r10k_run.html


[param_git]: ./r10k_custom.html#gitsettings
[direnv]: {{puppet}}/environments.html
[upgrade]: ./upgrade_mono.html
[environ_dir]: {{puppet}}/environments_configuring.html
[answer_file]: ./install_automated.html

To manage your Puppet code and data with either Code Manager or r10k, you need a control repository on Git. This control repository is where code management stores code and data to deploy your environments.

### How the control repository works

Code management uses Git repository branches to create [environments][direnv]. Environments allow you to designate a node or node group to use a specific environment. For example, you could designate one node group to use the development environment and another to use the production environment. As you update the code in your control repo, code management tracks the state of that repo to keep each environment updated.

Each branch of the control repo is copied into a corresponding environment directory. For example, for branches named "production", "test", and "development", code management creates the environment directories `environments/production`, `environments/test`, and `environments/development`. Environment directories are located in the Puppet master's [environment path][env_path] within the code directory (`$codedir/environments`).

Your control repo **must** have a production branch. The production branch is the default branch for your control repo, and you'll base your other branches and environments on this production branch. If you are using multiple control repos, do not duplicate branch names.

> **Warning**: Enabling code management means that Puppet manages the environment directories and **existing environments are not preserved**. Environments with the same name as the new one are overwritten. Environments not represented in the control repo are erased. If you were using environments before, commit any necessary files or code to the appropriate new environment branch, or back them up somewhere *before* you start configuring code management.

## Set up your control repo

Code management relies on version control, so before you begin configuring or using either Code Manager or r10k, set up your control repository.

Maintain this control repo using a development machine that is separate from your Puppet master.

1. Set up an SSH key to authenticate your control repository.

    1. Generate a private SSH key to allow access to the control repository.

       This SSH key cannot require a password. Your development machine needs full read/write access to your repos, and the Puppet master needs read access.

       See GitHub's [Generating SSH keys](https://help.github.com/articles/generating-ssh-keys/) for instructions on generating an SSH key for your repo.

    1. Place the private key in `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`.

       This key must be in a location that the `pe-puppet` user can access. Verify that the file itself has the correct permissions for the `pe-puppet` user to use the key.

       The `pe-puppet` user is created during installation, so installation must be complete for this step. If you are preparing your control repo before installing, place the key in the directory after installation.

2. Create a repository in your Git account.

    Steps for creating a repository vary, depending on what Git host you are using (GitHub, GitLab, Bitbucket, or another provider). See your Git host's documentation for complete instructions.
    
    On [GitHub](https://help.github.com/articles/creating-a-new-repository/):

    1. Click **+** at the top of the page, and choose **New repository**.
    1. Select the account **Owner** for the repository.
    1. Name the repository (for example, `control-repo`).
    1. Note the repository's SSH URL for later use.

3. Clone the repository to your development machine.

   You can clone the empty repository you just created, or you can clone the Puppet [`control repo` template][control_repo_template], which provides a minimal example of a control repo. In either case, on the command line, run:

   `git clone <repository URL>`

4. If you cloned the Puppet `control repo` template, set your own repository as the default remote and push the template contents to it:

   1. Remove the template repository as the origin remote with `git remote remove origin`.
   2. Add the control repository you created as the origin remote with `git remote add origin <url of your github repository>`.
   3. Push the production branch of the repository from your machine up to your git server: `git push -u origin production`.

    If you cloned the control repo template, the default branch of your repo is already the production branch and you can skip to the [next step](#next-steps) of creating a Puppetfile in your production environment.

5. If you cloned your own empty repo, create a production branch and set it as your default branch:

    1. Make a commit to the repository. You can create an empty commit with `git commit --allow-empty -m "initial empty commit"`. You cannot change or create branches in Git until you have committed at least one change.
    1. Rename the 'master' branch to 'production' with `git branch -m master production`.
    1. Push your changes with `git push origin production`

After you've set up your control repo, create a [Puppetfile][puppetfile] for managing your environment content with code management.

If you already have a Puppetfile, you can now configure code management. Code management configuration steps differ, depending on whether you're using Code Manager (recommended) or r10k. For important information about the function and limitations of each of these tools, see the [Code Manager][code_mgr] and [r10k][r10k] pages. For configuration instructions, see the [Code Manager configuration page][code_mgr_config] or the [r10k configuration page][r10k_config].

## Add an environment

If you already have a Puppetfile in your production branch, create new environments by creating new branches based on your production branch.

Before you add environments to your control repo, you must have:

* Configured either Code Manager or r10k.
* Created a [Puppetfile][puppetfile] in the default (usually 'production') branch of your control repo.
* A code management deployment method (such as the `puppet code` command or a configured webhook).

On the production branch of your control repo directory, on the command line:

1. Create a new branch with `git branch <NAME-OF-NEW-BRANCH>`.
1. Check out the new branch with `git checkout <NAME-OF-NEW-BRANCH>`.
1. Edit the Puppetfile as needed for your new environment, and save your changes.
1. Commit your changes with `git commit -m "a commit message summarizing your change"`.
1. Push your changes with `git push origin <NAME-OF-NEW-BRANCH>`.
1. Deploy your environments as you normally would.

If you have set up a Code Manager [webhook][code_mgr_webhook] or [script][scripts] trigger, Code Manager deploys the environments automatically. Alternately, deploy the environments on the command line with [Code Manager][code_mgr_cli] or [r10k][r10k_deploy] commands.

## Delete an environment

Delete environments by deleting their branch from your control repo.

1. On the production branch of your control repo directory, on the command line, delete the environment's corresponding remote branch with `git push origin :<BRANCH-TO-DELETE>`. For example, `git push origin :oldbranch`.
1. Delete the local branch with `git branch -d <BRANCH-TO-DELETE>`.
1. Deploy your environments as you normally would.

If you have set up a Code Manager [webhook][code_mgr_webhook] or [script][scripts] trigger, Code Manager deploys the environments automatically. Alternately, deploy the environments manually via [Code Manager][code_mgr_cli] or [r10k][r10k_deploy].

> **Note**: If you deploy environments with the Code Manager webhook, deleting a branch from your control repository does not immediately delete that environment from the Puppet master's live code directories.
>
> Code Manager will delete the environment when it next deploys changes to any other environment. Alternately, to delete the environment immediately, deploy all environments manually:
>
> ```
> puppet-code deploy --all --wait
> ```
