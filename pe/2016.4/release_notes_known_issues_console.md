---
layout: default
title: "PE console and console services known issues"
canonical: "/pe/latest/release_notes_known_issues_console.html"
---

This page lists known issues for the PE console and console services in Puppet Enterprise.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).

## Updated modes shown without leading zero in Events

When the [`mode` attribute for a `file` resource]({{puppet}}/file.html#file-attribute-mode) is updated, and numeric notation is used, leading zeros are dropped in the **New Value** field on the **Events** page. For example, `0660` is shown as "660" and `0000` is shown as "0." <!--PE-20109/PUP-7493--> 

## Node graph displays "unknown" status during a Puppet run on a newly compiled catalog 

If you view the node graph for a node with a newly compiled catalog during a Puppet run, the graph will have a status of "unknown" and will display grey status indicators. During a Puppet run, the catalog is submitted early in the run, but the report is generated at the end. Mid-run, the node's structure can be graphed, but its status is unknown. Once the Puppet run has completed, reopen the node graph to see the node's current status. <!--PE-16982-->

## Corrective change status for non-idempotent resources 

In certain situations, the enforcement of non-idempotent resource types can cause inaccurate reports of corrective change. Non-idempotent resources such as custom resource types or [exec resources]({{puppet}}/types/exec.html) that do not adhere to idempotency best practices might cause this behavior. The first time Puppet applies an exec resource it will be reported as an intentional change. All subsequent changes will be reported as corrective changes, even if the Puppet code for the exec resource was intentionally modified. <!--PE-17036--> 

## Node graph display errors for names containing non-ASCII characters  

The node graph rendering tool reads both individual non-ASCII characters and groups of non-ASCII characters as a single underscore. As a result, multiple graph elements might receive the same name, which are then conflated and incorrectly mapped. <!--PE-17096 see also utf-8 page-->

## Performance issues with the `unpin-from-all` endpoint 

As number of groups increases, response time can increase significantly with the `unpin-from-all` endpoint. <!--PE-14172-->

## Token generation error for new external directory users 

If a remote user who is part of an external directory group in RBAC attempts to generate an authentication token with a specific expiry prior to logging into the PE console for the first time, an error message will be returned. New remote users should log into the PE console prior to attempting to generate authentication tokens. <!--PE-12029-->

## Agent time skew results in incorrect error message 

If a PE agent attempts to check in with the master but the agent has significant time skew, an incorrect error message is returned. This message masks the real cause of the problem, which is the time skew. The error message says: `400 Error: "Attempt to assign to a reserved variable name: 'trusted' on node"`. <!--PE-6936-->





