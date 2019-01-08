---
title: "PE 2015.2 » Release Notes >> Known Issues"
layout: default
subtitle: "PE Console and Console Services Known Issues"
canonical: "/pe/latest/release_notes_known_issues_console.html"
---

As we discover them, this page will be updated with known issues related to the PE console and console services in Puppet Enterprise 2015.2 releases. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).

## Node Graph Display Errors for Names Containing Non-ASCII Characters  

The node graph rendering tool reads both individual non-ASCII characters and groups of non-ASCII characters as a single underscore. As a result, multiple graph elements might receive the same name, which are then conflated and incorrectly mapped. <!--PE-17096-->

## Count of Failed Login Attempts Does Not Reset After Reinstating a User Account

In cases where a user's account is revoked due to too many incorrect login attempts, and the account is reinstated via API by updating the `is_revoked` flag, the `failed_login_count` does not correctly reset, so the user's next incorrect login attempt re-revokes their account. <!--PE-16430-->

## Nodes Cannot Be Exported to a CSV File

In PE 3.8, the PE web UI had an option to export your list of nodes. This option is not available in PE 2015.2. There are still multiple ways to get node and report data from PE. You can obtain a list of managed nodes and a list of reports for a node in JSON format using the PuppetDB API.

To get a list of nodes, use the [`nodes` endpoint in the PuppetDB API](/puppetdb/2.3/api/query/v4/nodes.html).

To get a list of reports for a node, use the [`reports` endpoint in the PuppetDB API](/puppetdb/2.3/api/query/v4/reports.html).

If you only need the last report for each node, you can use the **Export data** button on the PE console UI. To access this button, in the console, go to **Configuration** > **Overview**.

## PE Console Does Not Recognize When External CA Is Used

If you use an [external CA to create certs for PE](./external_ca.html), the PE console may produce an error on the node inventory page, similar to `Error retrieving certificates: Array.prototype.map called on null or undefined`. This error appears because the console does not know an external CA has been used to create certs. This is safe to ignore and will be fixed in a future release.

## Batch Processing of Certificate Requests Does Not Complete if You Navigate Away From the PE Console

In PE 2015.2, the **Reject All** and **Accept All* buttons process certificate requests in batches. If you close the PE console browser window while either of these processes are in progress, the process is interrupted and only the current batch finishes processing.

## Multiple $ Signs in a Parameter Do Not Escape Properly

There are two specific circumstances in which escaping a $ sign does not work as expected.

1. If you use $ signs to specify the same variable multiple times within a string, and you escape at least one $ sign but not all of them, then the escaping will not work and all variables will be interpolated. For example, assuming that your hostname is "centos7," `"My \$hostname is $hostname."` should be interpolated as `"My $hostname is centos7."`, but instead, it is interpolated as `"My centos7 is centos7."`.

2. If you use a $ sign to specify a variable and then immediately follow it by one or more escaped $ signs in the same string, such as `"$one\$two"`, both `one` and `two` were escaped, even though `one` should have been interpolated as a variable.

## Custom Diffie-Hellman Param File Generation (part of "Log Jam Attack" mitigation)

To help mitigate the "Log Jam Attack," PE 2015.2 ships with a pre-generated 2048 bit Diffie-Hellman param file. In the case that you don't want to use the PE default DH param file, [Configuring the PE Console to Use a Custom Diffie-Hellman File](./trouble_dh_generate.html) shows how to generate your own.

## Creating Trusted Hash-Based Rules in the Classifier API Results in Unexpected Behavior

If you use the Classifier API to create a node group, and add a rule that uses a trusted hash, the matching nodes are displayed correctly in the **Matching nodes** tab of the PE console. However, if you go to **Nodes** > **Inventory**, click one of the matching nodes, and then go to the **Groups** tab, the node group is not listed.

##  Incorrect Error Message When Agent Has Significant Time Skew

If a PE agent attempts to check in with the master but the agent has significant time skew, an incorrect error message is returned. This message masks the real cause of the problem, which is the time skew. The error message says: `400 Error: "Attempt to assign to a reserved variable name: 'trusted' on node"`.

## Multiple RDNs When Connecting to an External Directory Service

You can only use one RBAC user RDN and one group RDN when connecting your existing external directory to PE. Use of multiple user RDNs or group RDNs is not supported.

## Custom Console Certs May Break on Upgrade

Upgrades to this version of PE can affect deployments that use a custom console certificate, as certificate functionality has changed between versions. Refer to [Configuring the Puppet Enterprise Console to Use a Custom SSL Certificate](./custom_console_cert.html) for instructions on re-configuring your custom console certificate.

## Nodes Can Be Pinned an Infinite Number of Times

When pinning a node to a node group in the PE console, if you pin the node multiple times, the console does not resolve this to a single entry in the **Rules** tab. For example, if you pin the same node to a node group ten times and commit it, the **Rules** tab will show that you have ten nodes all with the same node name. In the **Matching nodes** tab, these entries are consolidated into a single entry.

## Error When a PE User and User Group Have the Same Name

If you have both a PE user and an external directory user group with the exact same name, PE will throw an error when you try to log on as that user or import the user group. To work around this problem, you can change your settings to use different RDNs for users and groups. This will work as long as all of your users are contained under one RDN that is unique from the RDN that contains all of your groups ([also see the known issue regarding Multiple RDNs](#multiple-rdns-when-connecting-to-an-external-directory-service)).

## Querying the Nodes Endpoint of the Classifier Service Can Exhaust Memory

If a large number of nodes is returned when querying the `v1/nodes` endpoint of the classifier service API, the pe-console-services process may exhaust the memory and return a 500 error. This can be resolved by pruning the node check-in data as documented in [Configuring & Tuning Node Classifier](./config_nc.html).

## External Directory Groups Cannot Be Imported When Using SSL Certificate Verification

If you [configure PE to verify directory server certificates](./rbac_ldap.html#verify-directory-server-certificates), PE will fail to import user groups. This is due to an incorrect value that RBAC attempts to pass to the directory server. It does not affect the importation of users. You can still connect to the directory server using SSL for an encrypted connection and successfully import user groups.

### Login Attempts Can Time Out For Active Directory Users

When PE detects an Active Directory that supports the LDAP\_MATCHING\_RULE\_IN\_CHAIN feature, it automatically uses it. Under some specific circumstances, you may need to disable this setting. Specifically, if you find that login attempts by your Active Directory users are timing out, try disabling this setting. To disable it, follow the instructions in the [documentation for the directory service endpoint of the RBAC Service API](./rbac_dsref.html#put-ds).

## Expired or Deactivated Nodes Are Not Displayed in Overview After Seven Days

By default, expired and deactivated nodes are no longer displayed or accessible via the **Overview** page after 7 days. You can adjust the PuppetDB setting to increase the number of days that these types of nodes are viewable using the [`node-ttl` setting](./puppetdb/latest/configure.html#node-ttl).

## Resource Events Shows Change Count for Number of Nodes and Not Number of Events

On the **Events** page, the change count shows the number of distinct nodes with events for a resource, rather than the number of actual events.