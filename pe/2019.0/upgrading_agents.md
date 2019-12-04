# Upgrading agents

Upgrade your agents as new versions of Puppet Enterprise become available. The puppet\_agent module helps automate upgrades, and provides the safest upgrade. Alternatively, you can upgrade individual nodes using a script.

**Note:** Before upgrading agents, first upgrade your master and verify that the master and agent software versions are compatible. Then after upgrade, run Puppet on your agents as soon as possible to verify that agents have the correct configuration and that your systems are behaving as expected.

## Setting your desired agent version

To upgrade your master but use an older agent version that is still compatible with the new master, define a `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>` class with the `agent_version` variable set to your desired agent version.

To ensure your agents are always running the same version as your master, in the puppetlabs-puppet\_agent module, set the `package_version` variable for the `puppet_agent` class to `auto`. This causes agents to automatically upgrade themselves on their first Puppet run after a master upgrade.

**Related information**  


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

        To specify a particular agent version, set the `agent_version` variable using an `X.Y.Z` format \(for example, `5.5.14`\). When their version is set explicitly, agents do not automatically upgrade when you upgrade your master.

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
    |**package\_version**|The `puppet-agent` package version to install, for example 5.3.3.Set this parameter to `auto` to install the same agent version that is installed on your master.

|

8.  On the agents that you're upgrading, run Puppet: `/opt/puppet/bin/puppet agent -t`


### Results

After the Puppet run, you can verify the upgrade with `/opt/puppetlabs/bin/puppet --version`

## Upgrade a \*nix or Windows agent using a script

To upgrade an individual node, for example to test or troubleshoot, you can upgrade directly from the node using a script. This method relies on a package repository hosted on your master.

### About this task

**Note:** If you encounter SSL errors during the upgrade process, ensure your agent's OpenSSL is up to date and matches the master's version. You can check the master's OpenSSL versions with `/opt/puppetlabs/puppet/bin/openssl version` and the agent's version with `openssl version`.

### Upgrade a \*nix agent using a script

You an upgrade an individual \*nix agent using a script.

#### Procedure

1.  Configure the master to download the agent version you want to upgrade.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

    2.  On the **Configuration** tab, in the **Add a new class** field, enter `pe_repo`, and select the appropriate repo class from the list of classes.

        Repo classes are listed as `pe_repo::platform::<AGENT_OS_VERSION_ARCHITECTURE>`.

        To specify a particular agent version, set the `agent_version` variable using an `X.Y.Z` format \(for example, `5.5.14`\). When their version is set explicitly, agents do not automatically upgrade when you upgrade your master.

    3.  Click **Add class** and commit changes.

    4.  On your master, run Puppet to configure the newly assigned class: `puppet agent -t`

        The new repo is created in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/<PLATFORM>/`.

2.  SSH into the agent node you want to upgrade.

3.  Run the upgrade command appropriate to the operating system.

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

### Upgrade a Windows agent using a script

You can upgrade an individual Windows agent using a script. For Windows, this method is riskier than using the puppet\_agent module to upgrade, because you must manually complete and verify steps that the module handles automatically.

#### About this task

**Note:** The `<MASTER HOSTNAME>` portion of the installer script—as provided in the following example—refers to the FQDN of the master. The FQDN must be fully resolvable by the machine on which you're installing or upgrading the agent.

#### Procedure

1.  Stop the Puppet service and the PXP agent service.

2.  On the Windows agent, run the install script:

    ```
    [Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; $webClient = New-Object System.Net.WebClient; $webClient.DownloadFile('https://<MASTER HOSTNAME>:8140/packages/current/install.ps1', 'install.ps1'); .\install.ps1
    			 
    ```

3.  Verify that Puppet runs are complete.

4.  Restart the Puppet service and the PXP agent service.


## Upgrading the agent independent of PE

You can optionally upgrade the agent to a newer version than the one packaged with your current PE installation.

For details about Puppet agents versions that are tested and supported for PE, see the PE component version table.

The agent version is specified on a platform-by-platform basis in the **PE Master** node group, in any `pe_repo::platform` class, using the `agent_version` parameter.

When you install new nodes or upgrade existing nodes, the agent install script installs the version of the agent specified for its platform class. If a version isn’t specified for the node’s platform, the script installs the default version packaged with your current version of PE.

**Note:** To install nodes without internet access, download the agent tarball for the version you want to install, as specified using the `agent_version` parameter.

The platform in use on your master requires special consideration. The agent version used on your master must match the agent version used on other infrastructure nodes, including compilers and replicas, otherwise your master won’t compile catalogs for these nodes.

To keep infrastructure nodes synced to the same agent version, if you specify a newer `agent_version` for your master platform, you must either:

-   \(Recommended\) Upgrade the agent on your master—and any existing infrastructure nodes—to the newer agent version. You can upgrade these nodes by running the agent install script.
-   Manually install the older agent version used on your master on any new infrastructure nodes you provision. You **can’t** install these nodes using the agent install script, because the script uses the agent version specified for the platform class, instead of the master’s current agent version. Manual installation requires configuring `puppet.conf`, DNS alt names, CSR attributes, and other relevant settings.

**Related information**  


[Component versions in recent PE releases](component_versions_in_recent_pe_releases.md#)

