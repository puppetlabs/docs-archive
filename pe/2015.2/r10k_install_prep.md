---
layout: default
title: "PE 2015.2 » Code Management with r10k » Preparing for r10k Installation"
subtitle: "Preparing for r10k Installation"
canonical: "/pe/latest/r10k_install_prep.html"
description: "Preparing to install r10k code management with Puppet Enterprise."
---

[config_answer]: ./r10k_config_answers.html
[config_console]: ./r10k_config_console.html
[puppetfile]: ./r10k_puppetfile.html
[run]: ./r10k_run.html
[r10kindex]: ./r10k.md
[repomodule]: ./r10k_puppetfile.html#declaring-a-git-repo-as-a-module
[param_git]: ./r10k_config_console.html#gitsettings

[direnv]: /puppet/latest/reference/environments.html
[upgrade]: ./install_upgrading.html
[control_repo]: https://github.com/puppetlabs/r10k/blob/0e1dae13a6fe5db39f8a6e1ed941039fec818cc4/doc/common-patterns.mkd#repository-setup
[environ_dir]: /puppet/latest/reference/environments_configuring.html
[answer_file]: ./install_automated.html

Puppet Enterprise (PE) 2015.2 includes r10k, so you don't need to separately download or install it. Before you begin using r10k, however, you need to make sure it can communicate with Git.

If you've used r10k before, there are also a couple of [upgrade issues](#upgrading-from-earlier-versions-of-r10k) to note.

## r10k and Git

R10k uses your existing Git repository (repo) branches to create [directory environments][direnv]. Environments allow you to designate a node or node group to use a specific environment; for example, you could designate one node group to use the development environment and another to use the production environment. As you update the code in your control repo, r10k tracks the state of that repo to keep each environment updated.

Each branch of a connected repository is copied into a directory named after the branch. For instance, if your repository is called "myenvironments", with branches named "production", "test", and "development", r10k copies the production branch into a production directory, the test branch into a test directory, and the development branch into a development directory. It's important to remember that because r10k names each new directory after the branch in your Git repo, **if your directory environment has the same name as the one r10k is creating, r10k will erase EVERYTHING in your previous directory**.

>**Warning:** If you were using directory environments without r10k, you must make sure that any necessary files or code are either committed to the appropriate Git repo or backed up somewhere. The directory environments location is entirely managed by r10k, and any contents that r10k did not put there will be **removed**.
>
>To avoid losing any of your existing modules, back them up from their environment directories before running r10k. Then place each module in its own repo and add these module repositories [to your Puppetfile][repomodule]. 

## Set Up Your Git Repo

For r10k to work, it needs to be able to communicate with Git. So before you begin configuring or using r10k, you'll need:

* A ["control" repository][control_repo] for your Puppet code and Puppetfile. If you don't already have this, then [set up at least one repository](http://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository) to act as a repo for your code. This control repo should be maintained using a development machine that is separate from your Puppet master.

* A private ssh key to allow access to this control repository. See Github's [Generating SSH keys](https://help.github.com/articles/generating-ssh-keys/) for details on generating an SSH key for your repos. The development machine needs full read/write access to your repos, while the Puppet master needs read access.

A Git library is already included with r10k, so you don't need to install Git on your system, unless you plan to use the [shellgit][param_git] provider. If you are using shellgit, [install Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git) on both your Puppet master and on the development machine that will host your control repo.

## Next Steps

There are two paths for configuring r10k:

* With a fresh PE installation, configure r10k automatically through the answer file.
* With an existing PE installation, configure r10k in the PE console. This method also allows more complex r10k configurations.

### Configure r10k with a New PE Installation

If you provide an [answer file][answer_file] during a fresh command line installation of Puppet Enterprise 2015.2 or later, you can add answers for r10k configuration. See the [Installing r10k with the Answer File][config_answer] for details and important information about this configuration method.

If you do not provide an answer file during installation, or you installed PE using web-based installation, you will need to configure r10k [in the PE console][config_console].

### Configure r10k with an Existing PE installation

If you're setting up or otherwise changing r10k after PE installation, as with an upgrade, you'll configure r10k in the [PE console][config_console]. 

If you're already using r10k with earlier versions of PE or open source Puppet, and you are [upgrading to PE 2015.2][upgrade], we recommend that you [switch](#upgrading-from-earlier-versions-of-r10k) to the r10k functionality built into PE 2015.2.

#### Upgrading from earlier versions of r10k

PE 2015.2 includes the pe_r10k module, which allows you to configure r10k in the PE console instead of manually managing r10k's configuration file. This enables you to automate r10k configuration for new Puppet masters and prepares you for future versions of r10k.

To switch to r10k and the pe_r10k module as included with Puppet Enterprise 2015.2, find your existing r10k.yaml, and then set the corresponding settings in PE console. If you were using r10k with PE 3.8, this file is `/etc/puppetlabs/r10k/r10k.yaml`. If you were using r10k with any other version of PE or Puppet, this file is `/etc/r10k.yaml`. See [Configuring r10k in the PE Console][config_console] for available settings and details.

> Note: If you were using earlier versions of r10k with the zack-r10k module, we encourage you to switch to the built-in pe_r10k module for configuration as above. The zack-r10k module cannot be used with pe_r10k.
