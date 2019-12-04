# Upgrading agents

Upgrade your agents as new versions of Puppet Enterprise become available. The upgrade method you use depends on the agent operating system and the method used to install the agent.

**Note:** Before upgrading agents, first upgrade your master and verify that the master and agent software versions are compatible. Then after upgrade, run Puppet on your agents as soon as possible to verify that agents have the correct configuration and that your systems are behaving as expected.

**Related topics**  


[Upgrading Puppet Enterprise](upgrading_pe.md#)

## Upgrade \*nix or Windows agents using the puppet\_agent module

The puppetlabs-puppet\_agent module, available from the Forge, enables you to upgrade multiple agents at once. The module handles all the latest version to version upgrades.

### About this task

When upgrading agents, first test the upgrade on a subset of agents, and after you verify the upgrade, upgrade remaining agents.

### Procedure

1.  On your master, download and install the puppetlabs-puppet\_agent module: `puppet module install puppetlabs-puppet_agent`

2.  Configure the master to download the agent version you want to upgrade.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

    2.  On the **Configuration** tab, in the **Add a new class** field, enter `pe_repo`, and select the appropriate repo class from the list of classes.

        Repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`.

    3.  Click **Add class** and commit changes.

    4.  On your master, run Puppet to configure the newly assigned class: `puppet agent -t`

        The new repo is created in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/<PLATFORM>/`.

3.  Click **Classification**, click **Add group**, specify options for a new upgrade node group, and then click **Add**.

    -   **Parent name** — Select the name of the classification node group that you want to set as the parent to this group, in this case, **All Nodes**.

    -   **Group name** —Enter a name that describes the role of this classification node group, for example, agent\_upgrade.

    -   **Environment** — Select the environment your agents are in.

    -   **Environment group** — *Do not* select this option. 

4.  Click the link to **Add membership rules, classes, and variables**.

5.  On the **Rules** tab, create a rule to add the agents that you want to upgrade to this group, click **Add Rule**, and then commit changes.

    For example:

    -   **Fact** — **osfamily**

    -   **Operator** — **=**

    -   **Value** — RedHat

6.  Still in the agent upgrade group, click the **Configuration** tab, and in the **Add new class** field, add the **puppet\_agent** class, and click **Add class**.

    If you don’t immediately see the class, click **Refresh** to update the classifier.

    **Note:** If you’ve changed the prefix parameter of the **pe\_repo** class in your **PE Master** node group, set the **puppet-agent source** parameter of the upgrade group to https://<MASTER HOSTNAME\>:8140/<Prefix\>.

7.  In the **puppet\_agent** class, specify the version of the `puppet-agent` package version that you want to install, then commit changes.

    |**Parameter**|**Value**|
    |-------------|---------|
    |**package\_version**|The `puppet-agent` package version to install, for example 5.3.3.|

8.  On the agents that you're upgrading, run Puppet: `/opt/puppet/bin/puppet agent -t`


### Results

After the Puppet run, you can verify the upgrade with `/opt/puppetlabs/bin/puppet --version`

## Upgrade \*nix or Windows agents using PE package management

The master hosts a package repository used to install or upgrade agents in your infrastructure.

### About this task

If you're using PE package management, the `pe_repo`, hosted on the master, is upgraded when you upgrade the master.

**Note:** If you encounter SSL errors during the upgrade process, ensure your agent's OpenSSL is up to date and matches the master's version. You can check the master's OpenSSL versions with `/opt/puppetlabs/puppet/bin/openssl version` and the agent's version with `openssl version`.

### Upgrade \*nix agents installed with the installation script using PE package management 

If you installed your agents with the installation script, you can use the same script to upgrade them.

#### Procedure

1.  SSH into the agent node you want to upgrade.

2.  Run the upgrade command appropriate to the operating system.

    -   Most \*nix

        ```
        /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
        
        ```

    -   Mac OS X, Solaris, and AIX

        ```
        curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash		  
        ```


#### Results

PE services restarts automatically after upgrade.

### Upgrade \*nix agents installed without the installation script using PE package management

If you did not install your agents with the installation script, you can still use it to upgrade, but you must first configure your master to download the agent upgrade version.

#### Procedure

1.  Configure the master to download the agent version you want to upgrade.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

    2.  On the **Configuration** tab, in the **Add a new class** field, enter `pe_repo`, and select the appropriate repo class from the list of classes.

        Repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`.

    3.  Click **Add class** and commit changes.

    4.  On your master, run Puppet to configure the newly assigned class: `puppet agent -t`

        The new repo is created in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/<PLATFORM>/`.

2.  Run the upgrade command appropriate to the operating system.

    -   Most \*nix

        ```
        /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash
        
        ```

    -   Mac OS X, Solaris, and AIX

        ```
        curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER HOSTNAME>:8140/packages/current/install.bash | sudo bash		  
        ```


#### Results

PE services restarts automatically after upgrade.

### Upgrade Windows agents using PE package management

If you used PE package management to install Windows agents, you can use the same method to upgrade them.

#### About this task

**Note:** The `<MASTER HOSTNAME>` portion of the installer script—as provided in the following example—refers to the FQDN of the master. The FQDN must be fully resolvable by the machine on which you're installing or upgrading the agent.

#### Procedure

1.  On the Windows agent, run the install script:

    ```
    [Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; $webClient = New-Object System.Net.WebClient; $webClient.DownloadFile('https://<MASTER HOSTNAME>:8140/packages/current/install.ps1', 'install.ps1'); .\install.ps1
    			 
    ```


## Upgrade Windows agents using the .msi package

You can use the Windows agent .msi package to upgrade.

### Procedure

1.  [Download](https://puppet.com/download-puppet-enterprise) the appropriate new version of the .msi package.

2.  Follow the installer dialog.


### Results

The agent service restarts automatically after upgrade.

