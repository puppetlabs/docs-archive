# Adding and removing agent nodes

After you install a Puppet agent on a node, accept its certificate signing request and begin managing it with Puppet Enterprise \(PE\). Or remove nodes that you no longer need.

## Add nodes

When you install a Puppet agent on a node, the agent automatically submits a certificate signing request \(CSR\) to the master. You must accept this request to bring before the node under PE management can be added your deployment . This allows Puppet to run on the node and enforce your configuration, which in turn adds node information to PuppetDB and makes the node available throughout the console.

You can approve certificate requests from the PE console or the command line. If DNS altnames are set up for agent nodes, you must approve the CSRs on use the command line interface .

**Note:** Specific user permissions are required to manage certificate requests:

-   To accept or reject CSRs in the console or on the command line, you need the permission **Certificate requests: Accept and reject**.
-   To manage certificate requests in the console, you also need the permission **Console: View**.

**Related information**  


[Installing agents](installing_agents.md#)

### Managing certificate signing requests in the console

Open certificate signing requests appear in the console on the **Unsigned certs** page. Accept or reject submitted requests individually or in a batch.

-   To manage requests individually, click **Accept** or **Reject**.
-   To manage the entire list of requests, click **Accept All** or **Reject All**. Nodes are processed in batches. If you close the browser window or navigate to another website while processing is in progress, only the current batch is processed.

The node appears in the PE console after the next Puppet run. To make a node available immediately after you approve the request, run Puppet on demand.

**Related information**  


[Running Puppet on demand](running_puppet_on_demand_with_orch.md)

### Managing certificate signing requests on the command line

You can view, approve, and reject node requests using the command line.

To view pending node requests on the command line:

```
$ sudo puppetserver ca list

```

To sign a pending request:

```
$ sudo puppetserver ca sign <NAME>

```

To sign pending requests for nodes with DNS altnames:

```
$ sudo puppetserver ca sign (<HOSTNAME> or --all) --allow-dns-alt-names
```

## Remove nodes

If you no longer wish to manage a node, you can remove it and make its license available for another node.

### About this task

Removing a node:

-   Deactivates the node in PuppetDB.

-   Deletes the Puppet master’s information cache for the node.

-   Makes the license available for another node.

-   Makes the hostname available for another node.


**Note:** Removing a node doesn't uninstall the agent from the node.

### Procedure

1.  On the agent node, stop the agent service.

    -   Agent versions 4.0 or later: `service puppet stop`
    -   Agent versions earlier than 4.0: `service pe-puppet stop`
    **Note:** To see which version of Puppet is installed on a node, run `puppet --version`.

2.  On the master, purge the node: `puppet node purge <CERTNAME>`

    The node’s certificate is revoked, the certificate revocation list \(CRL\) is updated, and the node is deactivated in PuppetDB and removed from the console. The license is now available for another node. The node can't check in or re-register with PuppetDB on the next Puppet run.

3.  If you have compilers, run Puppet on them: `puppet agent -t`

    The updated CRL is managed by Puppet and distributed to compilers.

4.  If the node you’re removing was pinned to any node groups, you must manually unpin it from individual node groups or from all node groups using the `unpin-from-all` command endpoint.


**Related information**  


[Uninstall infrastructure nodes](uninstalling.md#)

[POST /v1/commands/unpin-from-all](commands_endpoint.md#)

### Uninstall \*nix agents

The \*nix agent package includes an uninstall script, which you can use when you're ready to retire a node.

#### Procedure

1.  On the agent node, run the uninstall script: `run /opt/puppetlabs/bin/puppet-enterprise-uninstaller`

2.  Follow prompts to uninstall.

3.  \(Optional\) If you plan to reinstall on the node at a later date, remove the agent certificate for the agent from the master: `puppetserver ca clean <AGENT CERT NAME>`


### Uninstall Windows agents

To uninstall the agent from a Windows node, use the Windows **Add or Remove Programs** interface, or uninstall from the command line.

#### About this task

Uninstalling the agent removes the Puppet program directory, the agent service, and all related registry keys. The data directory remains intact, including all SSL keys. To completely remove Puppet from the system, manually delete the data directory.

#### Procedure

1.  Use the Windows **Add or Remove Programs** interface to remove the agent.

    Alternatively, you can uninstall from the command line if you have the original .msi file or know the product code of the installed MSI, for example: `msiexec /qn /norestart /x [puppet.msi|<PRODUCT_CODE>]`

2.  \(Optional\) If you plan to reinstall on the node at a later date, remove the agent certificate for the agent from the master: `puppetserver ca clean <AGENT CERT NAME>`


