---
layout: default
title: "PE 2015.2 » Quick Start » r10k"
subtitle: "r10k Quick Start Guide"
canonical: "/pe/latest/quick_start_r10k.html"
---

## r10k Quick Start Guide

[r10k_diagram]: ./images/quick/r10k_qsg.svg
[config_console]: ./r10k_config_console.html

The primary purpose and benefit of r10k is that it provides a localized place in Puppet for you to manage the configuration of various environments (such as production, development, or testing), including what specific versions of modules are in each of your environments, based on code in branches of Git repositories. R10k connects the code in your Git repository's branches with the Puppet environments it creates based on those branches. So the work you do in Git is reflected in your Puppet configuration.

In this guide you'll version the code you wrote for the Hello, World! module for two different environments: one called `production` and the other `test_message`. Each of these environments will map to a specific branch named after the environment in a repo called **puppet-control**. Each branch in the **puppet-control** repo will contain a `Puppetfile` that references the URL for the Hello, World! module repo and the latest commit ID for the code changes. When you run r10k on the Puppet master, r10k reads the `Puppetfile` and pulls down different versions of the Hello, World! module, a version of the module for each branch (or environment).

The following diagram outlines this workflow.

![r10k diagram][r10k_diagram]

This guide is a simple proof-of-concept primer for getting to know r10k. For more information, see the main [r10k docs](./r10k.html).

> ## Assumptions and Prerequisites
>
> Before beginning, please review the following assumptions and prerequisites.
>
>- For this guide, we assume you have the following machines available:
>
>   - A Puppet master running on a monolithic or split installation of PE 2015.2 (`master.example.com`). Note that this guide uses the PE console.
>   - A development machine for writing Puppet code and versioning it with Git (`dev.example.com`).
>   - A Puppet agent (`agent.example.com`) that can communicate with your master. Ensure that time is synced between your Puppet agent and your Puppet master, and that these machines can reach each other by name. This can be done with a local DNS server or by editing the `/etc/hosts` file on each machine.
>
>- Git is installed on the development machine and the Puppet master server and you know how to do basic tasks with it.
>- You have created two version-controlled repositories.
>- You do not already have r10k configured via the answer file or master profile.
>
>   For ease of use, call the repos **puppet-control** and **puppet-helloworld**. The user on the development machine needs full read/write access to these repos, and the Puppet master needs read access. See Github's [Generating SSH keys](https://help.github.com/articles/generating-ssh-keys/) for details on setting up SSH access to your repos.
>- Clone the **puppet-control** and **puppet-helloworld** repos to working directories on the development machine:
>
>   - `$working-dir/puppet-control`
>
>   - `$working-dir/puppet-helloworld`
>
>- Complete the [Hello, World! Quickstart Guide](./quick_start_helloworld.html), and commit that module to the **puppet-helloworld** repo.

You'll perform the following major steps in this exercise:

- **Step 1**: Prepare the **puppet-control** repo.
- **Step 2**: Rename the master branch of the **puppet-control** repo.
- **Step 3**: Configure r10k on the Puppet master and run the `production` environment.
- **Step 4**: Version the code in the Hello, World module and create the `test_message` environment.
- **Step 5**: Deploy the `test_message` environment.
- **Step 6**: Test the environments on a Puppet agent node.


### Step 1: Prepare the **puppet-control** repo

Unless otherwise indicated, perform the steps in this section on your development machine.

 1. If you have not already done so, create a working directory and clone the **puppet-control** and **puppet-helloworld** repos into it.
 2. Navigate to the `puppet-helloworld` directory with `cd puppet-helloworld`.
 3. Gather the most recent good commit ID, which is a sha-1 hash that is unique for every Git commit. Run `git rev-parse HEAD` and copy the ID.
 4. Navigate to the `puppet-control` directory.
 5. In the `puppet-control` directory, create a file called `Puppetfile` by running `touch Puppetfile`. Note that this filename **must** start with a capital P.
 6. Open `Puppetfile` and edit it so that it contains the following Puppet code:

        forge "https://forge.puppetlabs.com/"

        mod 'helloworld',
          :git => 'https://github.com/<YOUR GITHUB ORGANIZATION or GITSERVER>/puppet-helloworld.git',
          :ref => '<COMMIT ID YOU COPIED IN STEP 3>'

    >**Note**: Double-check your code for syntax errors.

7. Save the file and exit.
8. Still in the `puppet-control` directory, run `mkdir manifests`.
9. **From the Puppet master**, copy `/etc/puppetlabs/code/environments/production/manifests/site.pp` and move it into the `manifests` directory created in Step 8 by running `scp /etc/puppetlabs/code/environments/production/manifests/site.pp user@admin.example.com:~/wip/puppet-control/manifests/`.
10. From the root of `puppet-control` create a file called `environment.conf` by running `touch environment.conf`.
11. Open `environment.conf` and edit it so that it contains the following Puppet code:

        modulepath = modules:$basemodulepath
        environment_timeout = 0

12. From the root of the `puppet-control` directory, run ` git status`. This shows you the files that you've created and edited, which are ready to be committed.
13. Run `git add --all`, and then run `git commit -m  "this is my initial commit"`.
14. Run `git push origin master`. This pushes your changes to the remote **puppet-control** repo.

### Step 2: Rename the master branch of the **puppet-control** repo to production

The default Puppet environment is `production`. You need to rename the **puppet-control** repo `master` branch to `production`, and then delete the `master` branch, because it will not map to any Puppet environments when you run r10k.

Perform the steps in this section on your development machine.

1. From the `puppet-control` directory, create a new branch called `production`by running `git checkout -b production`. This creates the `production` branch and changes to the new branch.

   >**Tip**: `git branch` lists all the branches in your repo, and the "*" indicates which branch you're on.

2. Push the new `production` branch to the remote **puppet-control** repo by running `git push origin production:production`.
3. Using Github.com, set the default branch to `production`. See [Setting the default branch](https://help.github.com/articles/setting-the-default-branch/).
4. From the `puppet-control` directory on your development machine, delete the `master` branch. Run `git branch -d master`. Note that you should not have changed any files in this directory since you last committed.

   >**Tip**: Run `git branch` to see that the `master` branch has been deleted. The branch will no longer be listed.

5. Delete the `master` branch from the remote **puppet-control** repo. From the `puppet-control` directory on your development machine, run `git push origin :master`.

   > **Note**: At this point, in the **puppet-control** repo, you have a `Puppetfile`, a `production` branch with `site.pp`, and a environment config file.

### Step 3: Configure r10k on the Puppet master and deploy the `production` environment

Perform the steps in this section on your Puppet master.

1. In the node manager, add the `pe_r10k` class to the PE master group.
2. In the `pe_r10k` class, set r10k's parameters with the keys and values listed in [Configuring r10k in the PE Console][config_console] as needed. At minimum, specify `remote` and the `git_settings`.

3. Run `puppet agent -t`.

   This Puppet run generates `/etc/puppetlabs/r10k/r10k.yaml`, a file containing the following code:

       cachedir: '/var/cache/r10k'

       sources:
         puppet:
          remote: 'https://github.com/<YOUR GITHUB ORGANIZATION or GITSERVER>/puppet-control'
           basedir: '/etc/puppetlabs/code/environments'

       git:
         private_key: "/root/.ssh/id_rsa"
          puppet:

            
   In this case, `remote` is the **puppet-control** repo, and `private_key` gives the location of the private SSH key. The basedir isn't specified, so it automatically defaults to the `$environmentpath`.
   
   Note that the `$environmentpath` defaults to `/etc/puppetlabs/code/environments` on a fresh installation, but if you are upgrading, this path might be different. To find out what your environment path is, run:
   
    
    puppet config print environmentpath --section master
    

4. Change to the r10k directory by running `cd /etc/puppetlabs/r10k`, and then run `r10k deploy environment -p -v`.

   This run of r10k contacts the **puppet-control** repo, and because of the contents of `Puppetfile`, pulls down the **puppet-helloworld** module that is on the `production` branch.

5. After the run completes, navigate to `/etc/puppetlabs/code/environments/` to see the `production` environment and that it contains the **puppet-helloworld** module.

6. Apply the **puppet-helloworld** module to the `production` environment by running `puppet apply -e 'include helloworld' --environment production`.

   You'll see the `notify` appear on the command line. To see r10k making the changes, `puppet apply -e 'include helloworld::motd'   --environment production`, and then run `cat /etc/motd`.

   >**Tip**: If there are any syntax errors in your Puppet code, you will be warned at this step. To correct errors:
>
>   1. Return to your development machine
>   2. Correct the Puppet code in the **puppet-helloworld** module.
>   3. Commit the change.
>   4. Gather the commit ID.
>   5. Update `Puppetfile` in the **puppet-control** repo with the commit ID.
>   6. Commit that change.
>   7. Re-deploy r10k from the Puppet master.

### Step 4: Version the code in the Hello, World module and create the `test_message` environment

In this step, you'll change the Hello, World! module so that it contains a new message for a new environment, and then create a new branch that maps to the new environment.

Perform the steps in this section on your development machine.

1. **On the development machine**, navigate to the `manifests` directory in `puppet-helloworld`.
2. Edit `init.pp` and `motd.pp` so that the content of the message is different. For example, change "Hello, world!" to "Hello, world! Welcome to Puppet Enterprise!".
3. Run `git add --all`, and then run `git commit -m "this is a commit to change the motd"`. Note that your commit message can be whatever you want, but make sure it describes the nature of the commit.
4. Run `git push origin master`.
5. Run `git log` and copy the most recent commit ID (which is the first commit ID listed).
6. Navigate to the `puppet-control` directory, and run `git branch` to ensure you're still on the `production` branch.
7. Run `git pull origin production` to ensure your local repo branch is up to date.
8. Still in the `puppet-control` directory, create a new branch called `test_message` by running `git checkout -b test_message`.

   >**Tip**: Run `git branch` to ensure the `test_message` branch was created.

9. Open `Puppetfile`, and for the `ref` line, paste in the commit ID you copied in step 5. (Be sure the commit ID is in single quotes; e.g., `:ref => <'COMMIT ID'>`.)
10. Save and close `Puppetfile`.
11. Still in the `puppet-control` directory, on the `test_message` branch, run `git add --all`, and then run `git commit -m  "this is a commit to update Puppetfile for the test_message branch"`.
12. Run `git push origin test_message:test_message`.

Once these changes are pushed up, the new `test_message` environment is available to deploy from the Puppet master.

### Step 5: Deploy the `test_message` environment

Perform the steps in this section on your Puppet master.

1. Change into the r10k directory by running `cd /etc/puppetlabs/r10k`, and run `r10k deploy environment -p -v`.

   This run of r10k contacts the **puppet-control** repo, and because of the contents of `Puppetfile` on each branch, pulls down the **puppet-helloworld** module for both `production` and `test_message` branches.

2. After the next Puppet run completes, navigate to `/etc/puppetlabs/code/environments/` to see the `production` and `test_message` environments, and see that they both contain the **puppet-helloworld** module. Navigate into the `manifests` directories and note the differences between the `motd.pp` files.

3. Apply the **puppet-helloworld** module to the `test_message` environment by running `puppet apply -e 'include helloworld' --environment test_message`.

   On this run, you'll see the new message you created for the `test_message` environment. You can also run `puppet apply -e 'include helloworld::motd' --environment test_message` followed by `cat /etc/motd`. Additionally, run the `production` environment again to see the original message.

   >**Tip**: As mentioned previously, if there are any syntax errors in your Puppet code, you will be warned at this step. To correct errors, return to your development machine, correct the Puppet code in the **puppet-helloworld** module, commit the change, gather the commit ID, update `Puppetfile` in the **puppet-control** repo with the commit ID, commit that change, and then re-deploy the r10k from the Puppet master.

   At this point, you're ready to test your environments on a Puppet agent.

### Step 6: Test the environments on a Puppet agent node

In this section, use the PE console and a Puppet agent (`agent.example.com`) to verify the work you've done up to this point. Before you test the module on your Puppet agent node, use the PE console's node manager to set up an environment group for the `test_message` environment and a few node groups to classify your agent node.

> **Tip**: Instead of using just one Puppet agent node in this section, you could use two and set each node to a different environment per the following instructions.

1. In the PE console, create a group called **Helloworld Test Environment**:

   a. Navigate to **Nodes** > **Classification**, and in the **Node group name** field, type __Helloworld Test Environment__. Click **Add group**.

   b. Select the **Helloworld Test Environment**, and on the **Rules** tab, in the **Certname** field, add the name of your agent node, `agent.example.com`.

   c. Click **Pin node**.

   d. Click **Edit node group metadata**.

   e. Enable the **Override all other environments** option.

   f. From the **Environment** drop-down list, select **test_message**. From the  **Parent** drop-down list, select **Production**.

   g. Click **Commit changes**.

2. Create a group called **Helloworld Test Message**.

   a. In the **Node group name** field, type **Helloworld Test Message**. From the **Environment** drop-down list, select **test_message**. Click **Add group**.

   b. Select the **Helloworld Test Message** group, and on the **Classes** tab, in the **Add new class** field, type `helloworld` and click **Add class**. Repeat this to add the `helloworld::motd` class as well.

   c. On the **Rules** tab, in the **Certname** field, add the name of your agent node `agent.example.com`, and click **Pin node**.

   d. Click **Commit changes**.

3. On the agent, run `puppet agent -t`.

   When Puppet runs, the notify message for the test_message environment will display on the command line.

4. After the agent run is complete, run `cat /etc/motd` to see your motd for the `test_message` environment.

5. Create a new node group called **Helloworld Production** and, following the steps above, assign it to the `production` environment. Add the `helloworld` and `helloworld::motd` classes, pin the Puppet agent node to the group, and commit the changes.
6. Remove the Puppet agent node from **Helloworld Test Environment** and **Helloworld Test Message** groups, and pin it to **Helloworld Production** group.
7. On the Puppet agent node, run `puppet agent -t`.

   When Puppet runs, the notify for the production environment will display on the command line.

8. After the agent run is complete, run `cat /etc/motd` to see your motd for the `production` environment.

## Next Steps

This QSG serves as a primer for getting to know r10k. For information about implementing more complicated patterns and workflows, check out the [main r10k docs](./r10k.html).







