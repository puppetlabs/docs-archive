---
layout: default
title: "PE console and console services known issues"
canonical: "/pe/latest/release_notes_known_issues_console.html"
---

This page lists known issues for the PE console and console services in Puppet Enterprise.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).

## **New:** Node match rules option does not properly refresh

When using the **Rules** tab in the console, the radio buttons for **Nodes must match all rules** or **Nodes may match any rule** do not always appear to maintain the setting you apply. For example, if you create multiple rules and select **Nodes may match any rule**  and then leave the page, the setting switches back to **Nodes must match all rules**. 

This is an error in the UI only: the rules you set are applied correctly. <!--PE-20055-->

## **New:** Updated modes shown without leading zero in Events

When the [`mode` attribute for a `file` resource]({{puppet}}/file.html#file-attribute-mode) is updated, and numeric notation is used, leading zeros are dropped in the **New Value** field on the **Events** page. For example, `0660` is shown as "660" and `0000` is shown as "0." <!--PE-20109/PUP-7493--> 

## RBAC tokens from previous versions not supported in 2017.1.1

RBAC tokens generated from previous PE versions do not work with 2017.1.1. To use services that require a token, each user needs to [generate a new token](./rbac_token_auth.html#generating-a-token-using-puppet-access). <!--PE-19991-->

## Node graph displays "unknown" status during a Puppet run on a newly compiled catalog 

If you view the node graph for a node with a newly compiled catalog during a Puppet run, the graph will have a status of "unknown" and will display grey status indicators. During a Puppet run, the catalog is submitted early in the run, but the report is generated at the end. Mid-run, the node's structure can be graphed, but its status is unknown. Once the Puppet run has completed, reopen the node graph to see the node's current status. <!--PE-16982-->

## Corrective change status for non-idempotent resources 

In certain situations, the enforcement of non-idempotent resource types can cause inaccurate reports of corrective change. Non-idempotent resources such as custom resource types or [exec resources]({{puppet}}/types/exec.html) that do not adhere to idempotency best practices might cause this behavior. The first time Puppet applies an exec resource it will be reported as an intentional change. All subsequent changes will be reported as corrective changes, even if the Puppet code for the exec resource was intentionally modified. <!--PE-17036--> 

## Performance issues with the `unpin-from-all` endpoint 

As number of groups increases, response time can increase significantly with the `unpin-from-all` endpoint. <!--PE-14172-->

## Agent time skew results in incorrect error message 

If a PE agent attempts to check in with the master but the agent has significant time skew, an incorrect error message is returned. This message masks the real cause of the problem, which is the time skew. The error message says: `400 Error: "Attempt to assign to a reserved variable name: 'trusted' on node"`. <!--PE-6936-->



