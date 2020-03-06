# Managing certificate signing requests

When you install a Puppet agent on a node, the agent automatically submits a certificate signing request \(CSR\) to the master. You must accept this request to bring before the node under PE management can be added your deployment. This allows Puppet to run on the node and enforce your configuration, which in turn adds node information to PuppetDB and makes the node available throughout the console.

You can approve certificate requests from the PE console or the command line. If DNS altnames are set up for agent nodes, you must approve the CSRs on use the command line interface .

**Note:** Specific user permissions are required to manage certificate requests:

-   To accept or reject CSRs in the console or on the command line, you need the permission **Certificate requests: Accept and reject**.
-   To manage certificate requests in the console, you also need the permission **Console: View**.

## Managing certificate signing requests in the console

Open certificate signing requests appear in the console on the **Unsigned certs** page. Accept or reject submitted requests individually or in a batch.

-   To manage requests individually, click **Accept** or **Reject**.
-   To manage the entire list of requests, click **Accept All** or **Reject All**. Nodes are processed in batches. If you close the browser window or navigate to another website while processing is in progress, only the current batch is processed.

The node appears in the PE console after the next Puppet run. To make a node available immediately after you approve the request, run Puppet on demand.

## Managing certificate signing requests on the command line

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

