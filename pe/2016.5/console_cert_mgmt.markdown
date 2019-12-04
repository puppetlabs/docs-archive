---
layout: default
title: "Managing node requests"
canonical: "/pe/latest/console_cert_mgmt.html"
---


### Overview

When you [install a new PE agent](./install_agents.html), the agent will automatically submit a certificate signing request (CSR) to the Puppet master. In the PE console, you can view a list of the nodes that have submitted CSRs, and approve or deny the requests individually or in a batch.

### Viewing node requests

To view the number of pending node requests in the PE console, go to **Nodes** > **Unsigned Certificates**.

You will see a list of any pending node requests. Each item on the list shows the node's name and the fingerprint of the corresponding CSR. (On smaller screens, if the display of the full fingerprint is truncated you can hover over it to see the entire fingerprint.)

### Rejecting and approving nodes

To accept or reject a CSR, you need the following two permissions:

* Console: View
* Certificate requests: Accept and reject

For information about user permissions, see [About RBAC Permissions](./rbac_permissions.html).

Use the buttons to accept or reject a single node, or all nodes. Note that once a node request is approved, the node will not show up in the console until the next Puppet run takes place. This could be as long as 30 minutes. It can also take up to two seconds per request for **Reject All** or **Accept All** to finish processing.

> **Note:** When using **Accept All** or **Reject All**, nodes are processed in batches. If you close the browser window or navigate to another website while processing is in progress, only the current batch will be processed.

In some cases, DNS altnames may be set up for agent nodes. In such cases, you cannot use the console to approve/reject node requests. The CSR for those nodes must be accepted or rejected using the CLI. From the Puppet master, run `sudo puppet cert sign (<HOSTNAME> or --all) --allow-dns-alt-names`.

In some cases, attempting to accept or reject a node request will result in an error. This is typically because the request has been modified somehow, usually by being accepted or rejected elsewhere (e.g. by another user or from the CLI).

### Working with requests from the CLI

You can still view, approve, and reject node requests using the command line interface.

To view pending node requests in the CLI, run:

    $ sudo puppet cert list

To sign one of the pending requests, run:

    $ sudo puppet cert sign <name>


