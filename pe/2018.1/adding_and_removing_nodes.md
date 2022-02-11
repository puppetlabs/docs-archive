# Adding and removing nodes

To manage nodes with Puppet Enterprise \(PE\), you must approve the node’s certificate signing request. If you no longer wish to manage a node, you can remove all traces of it from PE

## Managing certificate signing requests

When you install a new PE agent, the agent automatically submits a certificate signing request \(CSR\) to the master.

Certificate requests can be signed from the console or the command line. If DNS altnames are set up for agent nodes, you must use the command line interface to approve and reject node requests.

After approving a node request, the node doesn’t show up in the console until the next Puppet run, which can take up to 30 minutes. You can manually trigger a Puppet run if you want the node to appear immediately.

To accept or reject CSRs in the console or on the command line, you need the permission **Certificate requests: Accept and reject**. To manage certificate requests in the console, you also need the permission **Console: View**.

### Managing certificate signing requests in the console

The console displays a list of nodes on the **Unsigned certs** page that have submitted CSRs. You can approve or deny CSRs individually or in a batch.

If you use the **Accept All** or **Reject All** options, processing could take up to two seconds per request.

When using **Accept All** or **Reject All**, nodes are processed in batches. If you close the browser window or navigate to another website while processing is in progress, only the current batch is processed.

### Managing certificate signing requests on the command line

You can view, approve, and reject node requests using the command line.

To view pending node requests on the command line:

```
$ sudo puppet cert list

```

To sign a pending request:

```
$ sudo puppet cert sign <name>

```

To sign pending requests for nodes with DNS altnames:

```
$ sudo puppet cert sign (<HOSTNAME> or --all) --allow-dns-alt-names`
```

## Remove nodes

To completely remove a node from PE, you must purge the node and revoke its certificate so that it doesn’t continue to check in.

### About this task

Removing a node:

-   Deactivates the node in PuppetDB.

-   Deletes the Puppet master’s information cache for the node.

-   Frees up the license that the node was using.

-   Allows you to re-use the hostname for a new node.


**Note:** Purging a node doesn't uninstall the agent from the node.

### Procedure

1.  On the agent node, stop the agent service.

    -   Agent versions 4.0 or later: `service puppet stop`

    -   Agent versions earlier than 4.0: `service pe-puppet stop`

    **Note:** You can run `puppet --version` to see which version of Puppet you’re using.

2.  On the master, purge the node: `puppet node purge <CERTNAME>`

    The node’s certificate is revoked, the certificate revocation list \(CRL\) is updated, and the node is deactivated in PuppetDB and removed from the console, increasing your license count. The node can't check in or re-register with PuppetDB on the next run.

3.  If you have compile masters, run Puppet on them: `puppet agent -t`

    The updated CRL is managed by Puppet and distributed to compile masters.

4.  \(Optional\) If the node you’re removing was pinned to any node groups, you must manually unpin it from individual node groups or from all node groups using the `unpin-from-all` command endpoint.

5.  \(Optional\) If the node still exists but you no longer want to manage it, stop MCollective on the node:

    1.  Uninstall the agent or stop the MCollective service:

        -   Agent versions 4.0 or later: `service mcollective stop`

        -   Agent versions earlier than 4.0: `service pe-mcollective stop`

    2.  Remove the node’s certificate in `/etc/puppetlabs/mcollective/ssl/clients`.


**Related information**  


[Uninstall agents](uninstalling.md#)

[POST /v1/commands/unpin-from-all](commands_endpoint.md#)

