# Start installing \*nix agents

When a node is managed by Puppet, it runs a Puppet agent application, commonly called an *agent*. In this guide you'll install a \*nix Puppet agent, which regularly pulls configuration catalogs from a Puppet master and applies them locally. This section includes how to sign the agent certificate request in the console.

These instructions assume that you've installed a monolithic PE deployment and have the Puppet master, the PE console, and PuppetDB up and running on one node.

## How does a Puppet agent work?

Agents ensure that resources in a node stay in their desired state.

Periodically, a Puppet agent will send facts to a Puppet master and request a catalog. The master compiles the catalog using several sources of information, and returns the catalog to the agent.

Once it receives a catalog, the agent applies it by checking each resource the catalog describes. If it finds any resources that are not in their desired state, the agent will make any changes necessary to correct them. \(Or, in no-op mode, it will report on what changes would have been needed.\)

After applying the catalog, the agent submits a report to its master. Reports from all the agents are stored in PuppetDB and can be accessed in the PE console.

## Step 1a: Install an agent with the same OS and architecture as the Puppet master

*Follow these steps if your agent node and your Puppet master have the same OS and architecture. Otherwise, go to Step 1b.*

### Procedure

1.  Log into your agent node and run:

    ```
    curl -k https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash                
    ```

    This script detects the agent's operating system, sets up an apt, yum, or zipper repo that refers back to the Puppet master, and then pulls down and installs the `puppet-agent` packages.

2.  After installation is complete, approve the certificate request to approve the new agent's certificate request in the console.


### Results

## Step 1b: Install an agent with a different OS and architecture than the Puppet master

Follow these steps if your agent node and your Puppet master do not have the same OS and architecture. Otherwise, go to Step 1a.

### About this task

This example describes adding an agent running Debian 6 on AMD64 hardware. As you complete the steps, modify the commands to match your agent's OS and architecture.

### Procedure

1.  In the console, click **Classification**, and in the `PE Infrastructure` node group, select the `PE Master` group.

2.  On the **Configuration** tab, in the **Add new class** field, enter `pe_repo`, and select the class `pe_repo::platform::debian_6_amd64` — or whichever class matches your agent — from the list of classes.

    The repo classes are listed as `pe_repo::platform::<agent_os_version_architecture>`

3.  Click **Add class**.

    The class you selected now appears in the list of classes for the `PE Master` group, but it has not yet been configured on your nodes. For that to happen, you need to kick off a Puppet run.

4.  From the command line of your master, run `puppet agent -t`.

5.  SSH into your agent node, and run the following:

    `curl -k https://<master.example.com>:8140/packages/current/install.bash | sudo bash`

    If you want to install a version of PE other than the most recent release, replace `current` in the script with a specific PE version number, in the form of `2016.x.x`.

    Depending on your platform, the method for downloading the script might vary.

    The installer installs and configures the Puppet Enterprise agent.

6.  After installation is complete, approve the certificate request to approve the new agent's certificate request in the console.


### Results

## Step 2: Approve the certificate request

During installation, the agent node contacts the Puppet master and requests a certificate. To add the node to the console and to start managing its configuration, you must approve its certificate request.

### About this task

### Procedure

1.  In the console, load a list of currently pending node requests by clicking **Unsigned certs**.

2.  Click the **Accept All** button to approve the request and add the node.


### Results

The Puppet agent can now retrieve configurations from the master the next time Puppet runs.

## Step 3: Test the Puppet agent node

You can wait until Puppet runs automatically, or you can manually trigger Puppet runs.

### About this task

By default, the agent fetches configurations from the Puppet master every 30 minutes. \(You can configure this interval in the puppet.conf file with the `runinterval` setting.\) However, you can manually trigger a Puppet run from the command line at any time.

### Procedure

1.  On the agent, log in as root and run `puppet agent --test`. This triggers a single Puppet run on the agent with verbose logging.

    If you receive a `-bash: puppet: command not found` error, then the directory that PE installs its binaries in, `/opt/puppetlabs/bin`, isn’t included in your default `$PATH`. To include these binaries in your default `$PATH`, add them by running `PATH=/opt/puppetlabs/bin:$PATH;export PATH`.

2.  Note the long string of log messages, ending with this message: `Notice: Applied catalog in [...] seconds`.

    You are now fully managing the agent node! It has checked in with the Puppet master for the first time and received its configuration info. It will continue to check in and fetch new configurations every 30 minutes.


### Results

Next, you'll begin learning how to configure your agents with Puppet code, beginning with pre-built chunks of Puppet code called **modules**.

