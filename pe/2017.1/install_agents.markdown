---
layout: default
title: "Installing Puppet Enterprise agents"
canonical: "/pe/latest/install_agents.html"
---

Puppet Enterprise has package management tools to help you easily install and configure Puppet agents. You can also use standard *nix package management tools.

## How PE package management works

The Puppet master hosts a package repo used to install agents in your PE infrastructure.

The PE package management repo is created during installation of the Puppet master and serves packages over HTTPS using the same port as the Puppet master (8140). This means agents won't require any new ports to be open other than the one they already need to communicate with the Puppet master.

When you run the install script, it detects the OS on which it is running, sets up an apt, yum, or zipper repo that refers back to the Puppet master, and then pulls down and installs the `puppet-agent` packages. It also creates a basic `puppet.conf`, and kicks off a Puppet run.

Note that if the install script can't find agent packages corresponding to the agent's platform, it will fail with an error message telling you which `pe_repo` class you need to add to the master.


>**Warning**: Installing agents using the `pe_repo` class requires an internet connection. If you don't have access to the internet, refer to [installing agents in a Puppet Enterprise infrastructure without internet access](#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access).


## Installing Puppet agents with PE package management

PE provides its own package management to help you install Puppet agents in your PE infrastructure. 

The exact steps for agent installation are determined by the following:

- The target macine has the same OS and architecture as the Puppet master.
- The target machine has a different OS and architecture than the Puppet master. 
 
Before you begin, please note the following about these instructions:

- The `<PUPPET MASTER HOSTNAME>` portion of the installer script---as provided in the following examples---refers to the FQDN of the Puppet master. If you've already logged into the console, you can find the exact script with the correct Puppet master hostname for your installation by clicking on **node requests** in the top right-hand corner of the console. (You do not need to have pending node requests to click.)

- The steps for installing Windows agents with PE package management are different than those for *nix agents. Refer to the [installing Windows agent instructions](./install_windows.html#installing-with-pe-package-management) for Windows-specific instructions on installing agents with PE package management.

### Install agents with same OS and architecture as the Puppet master

If the Puppet master and node on which you're installing the agent have the same OS and architecture, you can install your agents without any additional configuration on your Puppet master.  

1. SSH into the node where you want to install the PE agent, and run the following command:

   ~~~
   curl -k https://<PUPPET MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
   ~~~
   
   >**Tip:** If the `curl` command on your platform does not support the `-k` option, refer to the [instructions on using `curl` against the Puppet master CA cert](#install-agents-without-curl-k).
   
   If you use `wget` instead of `curl`, run the following command:
   
   ~~~
   wget -O - -q --no-check-certificate --secure-protocol=TLSv1 https://<PUPPET MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
   ~~~
   
   If you use Solaris 10, run the following command as `root`:
   
   ~~~
   export PATH=$PATH:/opt/sfw/bin
   wget -O - -q --no-check-certificate --secure-protocol=TLSv1 https://<PUPPET MASTER HOSTNAME>:8140/packages/current/install.bash | bash
   ~~~   
      
   >**Tip**: See [passing configuration parameters to the install script](#passing-configuration-parameters-to-the-install-script) to specify configuration settings, such as specifying a cert name, which will be added to `puppet.conf`.

2. After the installation completes, [sign the agent certificates](#managing-agent-certificate-requests).

### Install agents with a different OS and architecture than the Puppet master

To install an agent with a different OS than the Puppet master, you add the appropriate class for the repo that contains the agent packages and then classify the PE Master node group with that class. You then run the install script from the agent to retrieve the necessary packages for installation. 

In the following example, the Puppet master is on a node running RHEL 6 and the agent is on a node running Debian 6 on AMD64 hardware.

> **Note**: If your Puppet master uses a proxy server to access the internet, refer to [this known issue](./release_notes_known_issues_install.html#install-agents-with-different-os-when-puppet-master-is-behind-a-proxy) for a workaround.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Masters** group.

2. On the **Classes** tab in the **Class name** field, enter `pe_repo` and select the repo class from the list of classes.

   >**Note**: the repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`

3. Click **Add class**, and commit changes.

4. Run `puppet agent -t` to configure the Puppet master node using the newly assigned class.

   The new repo is created in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/<PLATFORM>/`.

5. SSH into the node where you want to install the agent, and run the following command:

   ~~~
   curl -k https://<PUPPET MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
   ~~~
   
   >**Tip:** If the `curl` command on your platform does not support the `-k` option, refer to the [instructions on using `curl` against the Puppet master CA cert](#install-agents-without-curl-k).

   
   If you use `wget` instead of `curl`, run the following command:
   
   ~~~
   wget -O - -q --no-check-certificate --secure-protocol=TLSv1 https://<PUPPET MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
   ~~~
   
   If you use Solaris 10, run the following command as `root`:
   
   ~~~
   export PATH=$PATH:/opt/sfw/bin
   wget -O - -q --no-check-certificate --secure-protocol=TLSv1 https://<PUPPET MASTER HOSTNAME>:8140/packages/current/install.bash | bash
   ~~~   
      
   >**Tip**: See [passing configuration parameters to the install script](#passing-configuration-parameters-to-the-install-script) to specify configuration settings, such as specifying a cert name, which will be added to `puppet.conf`.

   The script installs the PE agent packages, creates a basic `puppet.conf`, and kicks off a Puppet run.

6. After the installation completes, [sign the agent certificates](#managing-agent-certificate-requests).

## Passing configuration parameters to the install script

When using PE package management to install agents, you can pass configuration settings to the agent install script.

On *nix-based systems, you can pass parameters to the end of the install script to specify configuration settings that will be added to `puppet.conf` and to specify settings for inclusion in `custom_attributes` and `extension_requests` sections of `csr_attributes.yaml`.

For example:

~~~
curl -k https://master.example.com:8140/packages/current/install.bash | sudo bash -s agent:certname=<certnameOtherThanFQDN> custom_attributes:challengePassword=<passwordForAutosignerScript> extension_requests:pp_role=<puppetNodeRole>
~~~

You can pass as many parameters as you need to. Follow the `section:key=value` pattern and leave one space between parameters.

Refer to the [Configuration Reference]({{puppet}}/configuration.html) for a complete list of values for `puppet.conf`. Visit the [CSR attributes and certificate extensions]({{puppet}}/ssl_attributes_extensions.html) page for more information on settings for `csr_attributes.yaml`.

## Install agents without `curl -k`

In the instructions for installing agents with PE package management, the `-k` flag is needed in order to get `curl` to trust the Puppet master, which it wouldn't otherwise since Puppet and its SSL infrastructure have not yet been set up on the node. 

If you choose not to use `curl -k`, you can transfer the Puppet master CA certificate to any machines you want to install agents on, and then run the installation script against that cert. 

>**Note:** Users of AIX 5.3, 6.1, and 7.1 should note that the `-k` is not supported. You should replace the `-k` flag with `-tlsv1` or `-1`. See the [system requirements](./sys_req_os.html#aix) for more information about AIX, curl, and OpenSSL.


1. On the machine that will become the agent node, create the following directory: `/etc/puppetlabs/puppet/ssl/certs`.
2. On the Puppet master, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and transfer `ca.pem` to the directory you made on the agent node in the previous step.    
3. On the agent node, make sure the file permissions are correct.

   ~~~
   chmod 444 /etc/puppetlabs/puppet/ssl/certs/ca.pem
   ~~~
   
4. When following the agent install instructions, use the --cacert flag to point to the Puppet master CA.

   ~~~
   curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
   ~~~   

## Installing agents in a Puppet Enterprise infrastructure without internet access

If your PE infrastructure does not have access to the internet beyond your infrastructure, you will not be able to fully use the agent installation instructions, as your Puppet master does not have access to PE's agent package distribution repo. Instead you can download the appropriate agent package, and then use one of the following options that best corresponds to your deployment needs.

- Use PE package management without internet access.
- Use your own package management without internet access, and optionally set your compile masters to install agents from your own management repo.

Before beginning, [download the appropriate agent package](https://puppet.com/misc/pe-files/pe_repo). 

### Use PE package management without internet access

The following instructions are for using PE package management to install agents when you don't have internet access beyond your infrastructure.

1. Copy the agent package into the `/opt/puppetlabs/server/data/staging/pe_repo-<PUPPET AGENT VERSION>` directory on your Puppet master.

2. Follow the steps in [Installing puppet agents with pe package management](#installing-puppet-agents-with-pe-package-management) to complete the process.

>**Note:** You must perform these steps any time you upgrade your Puppet master. 

### Use your own package management without internet access

The following instructions are for using your own package management to install agents when you don't have internet access beyond your infrastructure.

1. Add the agent package to your own package management/distribution system.
2. Use the PE console to disable the PE-hosted repo. 

   a. In the console, click **Nodes** > **Classification** and in the **PE Infrastructure** group, select the **PE Master** group. 
   
   b. On the **Classes** tab, find `pe_repo` class (as well as any class that begins `pe_repo::`), and click **Remove this class**. 
   
   c. Commit changes. 
   
>**Note:** You must perform these steps any time you upgrade your Puppet master. 

### Set compile masters to install agents from your own package management

If your infrastructure relies on compile masters to install agents, you don’t have to copy the agent package to each compile master. Instead, use the PE console to specify a path to the agent package on your package management server.

1. Add the agent package to your own package management/distribution system.
2. Set the `base_path` parameter of the `pe_repo` class to your package management server. 

   a. In the console, click **Nodes** > **Classification** and in the **PE Infrastructure** group, select the **PE Master** group.
   
   b. On the **Classes** tab, find `pe_repo` class and specify the parameter:
   
      - **Parameter** -- Select **base_path**.
      
      - **Value** -- Enter the fully qualified domain name of your package management server. 
         
   c. Click **Add parameter** and commit changes. 

## Installing agents with your own package management

If you choose not to use PE package management to install Puppet agents, you can use your own package management tools.

Agent packages can be found on the Puppet master in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/`. This directory contains the platform specific repository file structure for agent packages. By default this will contain a repository matching your Puppet master's OS/architecture. For example, if your Puppet master is running on CentOS 7, in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/`, you will find the directory `el-7-x86_64`, which contains the directories with all the packages needed to install an agent.

If your nodes are running an OS and/or architecture that is different from the master, download the appropriate agent package, extract the agent packages into the appropriate repo, and then install the agents on your nodes just as you would any other package (e.g., `yum install puppet-agent`).

Before you begin, [download the appropriate agent package](https://puppet.com/misc/pe-files/pe_repo).


1. Add the agent package to your package management repo. 

2. Configure the package manager on your agent node (Yum, Apt) to point to that repo.

3. Install the Puppet agent. 

   a. On Yum-based systems, run `sudo yum install puppet-agent`.
   
   b. On Apt-based systems, run `sudo apt-get install puppet-agent`.
   
4. Configure the agent using `puppet config set`. See [Configuring agents](#configuring-agents).

## Managing agent certificate requests

When you install a new PE agent, the agent will automatically submit a certificate signing request (CSR) to the Puppet master. Before you can fully manage an agent node, you must sign the agent's certificate request. 

Certificate requests can be signed from the PE console or the command line. 

Pending node requests are indicated in the main navigation bar, and requests can be signed from the PE console or the command line.

### Managing agent certificate requests from the PE console

After you install an agent, you can accept its certificate request from the PE console. 

The PE console, the **Unsigned Certificates** page provides a list of  nodes that have submitted CSRs, and approve or deny the requests individually or in a batch. 

To accept or reject a CSR, you need the following two permissions:

* Console: View
* Certificate requests: Accept and reject

For information about user permissions, see [About RBAC Permissions](./rbac_permissions.html). 

1. In the PE console, click **Nodes** > **Unsigned Certificates** to load a list of currently pending node requests.

2. Click the __Accept All__ or __Reject All__ button to approve or decline the signing requests.

   > **Note:** When using **Accept All** or **Reject All**, nodes are processed in batches. If you close the browser window or navigate to another website while processing is in progress, only the current batch will be processed.

Once a node request is approved, the node will not show up in the console until the next Puppet run takes place. This could be as long as 30 minutes. It can also take up to two seconds per request for **Reject All** or **Accept All** to finish processing.

### Managing agent certificate requests from the command line

After you install an agent, you can accept its certificate request from the command line.

1. View the list of pending certificate requests. On the command line of the Puppet master, run the following command:

   ~~~
   sudo puppet cert list
   ~~~

2. To sign a request for an agent, run one of the following commands:

   - If no DNS alt names are used:
   
     ~~~
     sudo puppet cert sign <AGENT CERT NAME>
     ~~~
   
   - If DNS alt names are used:
   
     ~~~
     sudo puppet cert sign (<HOSTNAME> or --all) --allow-dns-alt-names
     ~~~    
   
After signing a new node's certificate, it may take up to 30 minutes before that node appears in the console and begins retrieving configurations. You can use the CLI to trigger a Puppet run manually on the node if you want to see it right away.

## Configuring agents

After you sign its certificate, your agent is ready for management with PE. At this point you can add additional configurations to its `puppet.conf` file. 

You can add additional configurations by editing `/etc/puppetlabs/puppet/puppet.conf` directly, or by using [the `puppet config set` sub-command][config_set], which will edit `puppet.conf` automatically.

For example, to point the agent at a Puppet master called "master.example.com," run `puppet config set server master.example.com`. This will add the setting `server = puppetmaster.example.com` to the `[main]` section of `puppet.conf`. To set the certname for the agent, run `/opt/puppetlabs/bin/puppet config set certname agent.example.com`. For more details, see [the documentation for `puppet config set`][config_set].

>**Warning for Mac OS X users**: When performing a command line install of an agent on an OS X system, you must run  `/opt/puppetlabs/bin/puppet config set server` and `/opt/puppetlabs/bin/puppet config set certname` for the agent to function correctly.

## A note about binaries and symlinks

Refer to [Puppet Enterprise binaries and symlinks](./install_pe_mono.html#puppet-enterprise-binaries-and-symlinks) for information about the binaries and symlinks PE sets. You can also find instructions on disabling the symlinks.

## Preparing agents for orchestration services

If you have agents that will participate in Orchestration services, there are additional configuration steps you need to perform. For full instructions, see [enable cache catalogs for orchestration services](./orchestrator_install.html#enable-cache-catalogs-for-orchestration-services).

[config_set]: {{puppet}}/config_set.html

>**Tip**: After you finish installing your agents, follow the instructions in the [NTP Quick Start Guide](./quick_start_ntp.html) to have PE ensure time is in sync across your deployment.

