# Managing environments with a control repository

To manage your Puppet code and data with either Code Manager or r10k, you need a control repository on Git. This control repository is where code management stores code and data to deploy your environments.

## How the control repository works

Code management depends on the control repository to create and maintain environments.

Code management uses Git repository branches to create environments. Environments allow you to designate a node or node group to use a specific environment. For example, you could designate one node group to use the development environment and another to use the production environment. As you update the code in your control repo, code management tracks the state of that repo to keep each environment updated. To learn more about environments in Puppet, read the documentation about [environments](https://docs.puppet.com/puppet/5.3/environments_about.html).

Each branch of a connected repository is copied into a directory named after the branch. These environments are created in `/etc/puppetlabs/code/environments` on the master. For instance, if your repository is called "mycontrolrepo", with branches named "production", "test", and "development", your production branch becomes a production directory, the test branch becomes a test directory, and the development branch becomes a development directory.

Your control repo **must** have a production branch. The production branch is the default branch for your control repo, and you'll base your other branches and environments on this production branch. If you are using multiple control repos, do not duplicate branch names.

CAUTION:

Enabling code management means that Puppet manages the environment directories and **existing environments are not preserved**. Environments with the same name as the new one are overwritten. Environments not represented in the control repo are erased. If you were using environments before, commit any necessary files or code to the appropriate new environment branch, or back them up somewhere *before* you start configuring code management.

## Set up your control repo

Set up a control repository to allow r10k or Code Manager to manage your environments.

### Before you begin

Be sure to set up and maintain your control repo using a development machine that is separate from the master.

### Procedure

1.  Set up an SSH key to authenticate your control repository.

    1.  Generate a private SSH key to allow access to the control repository.

        This SSH key cannot require a password. Your development machine needs full read/write access to your repos, and the master needs read access. See the GitHub documentation for [generating SSH keys](https://help.github.com/articles/generating-ssh-keys/) for instructions on generating an SSH key for your repo.

    2.  Place the private key in `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`.

        This key must be in a location that the ``pe-puppet`` user can access. Verify that the file itself has the correct permissions for the `pe-puppet` user to use the key. This user is created during installation, so installation must be complete for this step. If you are preparing your control repo before installing PE, place the key in the directory after installation.

2.  Create a repository in your Git account.

    Steps for creating a repository vary, depending on what Git host you are using \(GitHub, GitLab, Bitbucket, or another provider\). See your Git host's documentation for complete instructions.

    For example, on GitHub:

    1.  Click **+** at the top of the page, and choose **New repository**.

    2.  Select the account **Owner** for the repository.

    3.  Name the repostory \(for example, `control-repo`\).

    4.  Note the repository's SSH URL for later use.

3.  Clone the repository to your development machine.

    You can clone the empty repository you just created, or you can clone the Puppet [`control repo`](https://github.com/puppetlabs/control-repo) template, which provides a minimal example of a control repo. In either case, on the command line, run:

    `git clone <repository URL>`

4.  If you cloned the Puppet`control repo` template, set your own repository as the default remote and push the template contents to it.

    1.  Remove the template repository as the origin remote with `git remote remove origin` on the command line.

    2.  Add the control repository you created as the origin remote with `git remote add origin <url of your github repository>` on the command line.

    3.  Push the production branch of the repository from your machine up to your Git server with `git push -u origin production` on the command line

    If you cloned the control repo template, the default branch of your repo is already the production branch and you can skip to the next step of creating a Puppetfile in your production environment.

5.  If you cloned your own empty repo, create a production branch and set it as your default branch.

    1.  Make a commit to the repository. You can create an empty commit with `git commit --allow-empty -m "initial empty commit"`. You cannot change or create branches in Git until you have committed at least one change.

    2.  Rename the 'master' branch to 'production' with `git branch -m master production`

    3.  Push your changes with `git push origin production`


### What to do next

After you've set up your control repo, create a Puppetfile for managing your environment content with code management.

If you already have a Puppetfile, you can now configure code management. Code management configuration steps differ, depending on whether you're using Code Manager \(recommended\) or r10k. For important information about the function and limitations of each of these tools, along with configuration instructions. see the Code Manager and r10k pages.

**Related topics**  


[How Code Manager works](code_mgr_how_it_works.md)

[Managing code with r10k](r10k.md)

## Add an environment

Create new environments by creating new branches based on the production branch of your control repository.

### Before you begin

Make sure you have:

-   Configured either Code Manager or r10k.

-   Created a Puppetfile in the default \(usually 'production'\) branch of your control repo.

-   Selected your code management deployment method \(such as the`puppet-code` command or a webhook\).


### Procedure

1.  Create a new branch by running `git branch <NAME-OF-NEW-BRANCH>`

2.  Check out the new branch by running `git checkout <NAME-OF-NEW-BRANCH>`

3.  Edit the Puppetfile as needed for your new environment, and save your changes.

4.  Commit your changes by running `git commit -m "a commit message summarizing your change"`

5.  Push your changes by running `git push origin <NAME-OF-NEW-BRANCH>`

6.  Deploy your environments as you normally would.

    If you have set up a Code Manager webhook or script trigger, it deploys the environments automatically when you push your changes. Alternatively, deploy the environments on the command line with Code Manager or r10k commands.


## Delete an environment with code management

To delete environments with Code Manager or r10k, delete the corresponding branch from your control repository.

### Procedure

1.  On the production branch of your control repo directory, on the command line, delete the environment's corresponding remote branch by running `git push origin :<BRANCH-TO-DELETE>``git push origin :oldbranch`

2.  Delete the local branch by running `git branch -d <BRANCH-TO-DELETE>`

3.  Deploy your environments as you normally would.


### Results

If you have set up a webhook or script trigger, Code Manager deploys the environments automatically. Alternatively, deploy the environments manually with Code Manager or r10k on the command line.

**Note:** If you deploy environments with the webhook, deleting a branch from your control repository does not immediately delete that environment from the master's live code directories. Code Manager will delete the environment when it next deploys changes to any other environment. Alternately, to delete the environment immediately, deploy all environments manually, run `puppet-code deploy --all --wait`

