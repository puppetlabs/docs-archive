---
layout: default
title: "PE UI and UI Services Known Issues"
canonical: "/pe/latest/release_notes_known_issues_console.html"
---

As we discover them, this page will be updated with known issues related to the PE UI and UI services in Puppet Enterprise 2015.3 releases. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).

## **New:** Node graph display errors for names containing non-ASCII characters  

The node graph rendering tool reads both individual non-ASCII characters and groups of non-ASCII characters as a single underscore. As a result, multiple graph elements might receive the same name, which are then conflated and incorrectly mapped. <!--PE-17096-->

## **New:** The Run Puppet option for individual nodes isn't available in large installations

In installations with large numbers of nodes, the **Run Puppet** button doesn't appear on the node detail pages. You can still run Puppet on individual nodes using the command line. <!--PE-16727-->

## **New:** Underscores in node names result in error

When loading the node detail page for a node with an underscore in its name, an error appears stating, "Error retrieving node agent list: Server Error." Additionally, the option to run Puppet doesn't appear, but you can still run Puppet on the node using the command line. <!--PE-14894-->

## **New:** Count of failed login attempts does not reset after reinstating a user account

In cases where a user's account is revoked due to too many incorrect login attempts, and the account is reinstated via API by updating the `is_revoked` flag, the `failed_login_count` does not correctly reset, so the user's next incorrect login attempt re-revokes their account. <!--PE-16430-->

## **New:** Directory service setting overwritten on upgrades from 2015.3.x

When upgrading from earlier versions in the 2015.3 series, the `disable_ldap_matching_rule_in_chain` directory service setting reverts to its default (`false`) setting. Follow the [instructions in the RBAC API docs](./rbac_dsref.html#put-ds) to change this setting to `true`. <!--PE-16354--> 

## Nodes Cannot Be Exported to a CSV File

In PE 3.8, the PE web UI had an option to export your list of nodes. This option is not available in PE 2015.3. There are still multiple ways to get node and report data from PE. You can obtain a list of managed nodes and a list of reports for a node in JSON format using the PuppetDB API.

To get a list of nodes, use the [`nodes` endpoint in the PuppetDB API](/puppetdb/3.2/api/query/v4/nodes.html).

To get a list of reports for a node, use the [`reports` endpoint in the PuppetDB API](/puppetdb/3.2/api/query/v4/reports.html).

If you only need the last report for each node, you can use the **Export data** button on the PE UI. To access this button, in the UI, go to **Configuration** > **Overview**.

## Users With Permissions to Edit Node Groups Can Change the Default Lifetime of an Authorization Token

The default lifetime of an authorization token can be configured through a parameter in the PE Console node group. Even if a user does not have the Override Default Lifetime permission in Role-Based Access Control (RBAC), they can still change the default lifetime if they have permission to edit parameters in the PE Console node group.

For more information on correctly configuring permissions to avoid unintentional access to token lifetime settings, see [Managing user access to the lifetime setting for authentication tokens](./rbac_permissions.html#managing-user-access-to-the-lifetime-setting-for-authentication-tokens).

## PE UI Does Not Recognize an External CA

If you use an external CA to create certs for PE, the Certificate Signing Request (CSR) will not appear in the PE UI. For information on working with an external CA, see [Using an External Certificate Authority With Puppet Enterprise](./external_ca.html).

## Batch Processing of Certificate Requests Does Not Complete if You Navigate Away From the PE UI

In PE 2015.3, the **Reject All** and **Accept All** buttons process certificate requests in batches. If you close the PE browser window while either of these processes are in progress, the process is interrupted and only the current batch finishes processing.

## Custom Diffie-Hellman Param File Generation (part of "Log Jam Attack" mitigation)

To help mitigate the "Log Jam Attack," PE ships with a pre-generated 2048 bit Diffie-Hellman param file. In the case that you don't want to use the PE default DH param file, [Configuring the PE Console to Use a Custom Diffie-Hellman File](./trouble_dh_generate.html) shows how to generate your own.

##  Incorrect Error Message When Agent Has Significant Time Skew

If a PE agent attempts to check in with the master but the agent has significant time skew, an incorrect error message is returned. This message masks the real cause of the problem, which is the time skew. The error message says: `400 Error: "Attempt to assign to a reserved variable name: 'trusted' on node"`.

## Multiple RDNs When Connecting to an External Directory Service

You can only use one RBAC user RDN and one group RDN when connecting your existing external directory to PE. Use of multiple user RDNs or group RDNs is not supported.

## Custom Console Certs May Break on Upgrade

Upgrades to this version of PE can affect deployments that use a custom console certificate, as certificate functionality has changed between versions. Refer to [Configuring the Puppet Enterprise Console to Use a Custom SSL Certificate](./custom_console_cert.html) for instructions on re-configuring your custom console certificate.

## Error When a PE User and User Group Have the Same Name

If you have both a PE user and an external directory user group with the exact same name, PE will throw an error when you try to log on as that user or import the user group. To work around this problem, you can change your settings to use different RDNs for users and groups. This will work as long as all of your users are contained under one RDN that is unique from the RDN that contains all of your groups ([also see the known issue regarding Multiple RDNs](#multiple-rdns-when-connecting-to-an-external-directory-service)).

## Multiple Users With an "Admin" Login Name in a Directory Service Will Block the PE Admin

If your directory contains multiple users with a login name of "admin," the PE admin account is unable to log in.

If you are locked out of PE as the admin user and there are no other users with administrator access that you can use to reset the access control settings in the UI, use cURL to disconnect from the directory service and allow admin access again.

Example cURL command:

`curl -X PUT --cert /etc/puppetlabs/puppet/ssl/certs/centos7.pem --key /etc/puppetlabs/puppet/ssl/private_keys/centos7.pem --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json" -d {} https://<DNS OF THE CONSOLE SERVER>:4433/rbac-api/v1/ds`

Alternatively, you can also resolve this issue by renaming one of the admin users in the directory or altering your directory server settings so that they exclude total-1 users with the "admin" login.

## Puppet May Run in Noop Mode Even if You Haven't Selected the Noop Option

If you set `noop = true` in a node's `puppet.conf` file, and then you attempt to run Puppet in the PE UI by going to **Nodes** > **Inventory** > &lt;NODE NAME&gt; > **Run Puppet**, Puppet will run in noop mode even if you do not select the **noop** option in the PE UI. This is because the `puppet.conf` file is the source of truth for the noop setting. To run Puppet from the PE UI, you need to remove the `noop = true` setting from the node's `puppet.conf` file.

## Expired or Deactivated Nodes Are Not Displayed After Seven Days

Expired and deactivated nodes are no longer displayed or accessible via the UI after 7 days by default. You can adjust the PuppetDB setting to increase the number of days that these types of nodes are viewable using the [`node-ttl` setting](./puppetdb/latest/configure.html#node-ttl).
