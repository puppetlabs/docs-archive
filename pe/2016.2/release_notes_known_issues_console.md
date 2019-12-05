---
layout: default
title: "PE console and console services known issues"
canonical: "/pe/latest/release_notes_known_issues_console.html"
---

This page lists known issues for the PE console and console services in Puppet Enterprise.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).

## **New:** Node graph display errors for names containing non-ASCII characters  

The node graph rendering tool reads both individual non-ASCII characters and groups of non-ASCII characters as a single underscore. As a result, multiple graph elements might receive the same name, which are then conflated and incorrectly mapped. <!--PE-17096-->

## `group-children` endpoint returns error for groups with no children

Calling the [`group-children` endpoint](./nc_groups_children.html) for a group with no children results in a server error, rather than the expected empty array. <!--PE-16200-->

## The Run Puppet option for individual nodes isn't available in large installations

In installations with large numbers of nodes, the **Run Puppet** button doesn't appear on the node detail pages. You can still run Puppet on individual nodes using the command line. <!--PE-16727-->

## Underscores in node names result in error

When loading the node detail page for a node with an underscore in its name, an error appears stating, "Error retrieving node agent list: Server Error." Additionally, the option to run Puppet doesn't appear, but you can still run Puppet on the node using the command line. <!--PE-14894-->

## Count of failed login attempts does not reset after reinstating a user account

In cases where a user's account is revoked due to too many incorrect login attempts, and the account is reinstated via API by updating the `is_revoked` flag, the `failed_login_count` does not correctly reset, so the user's next incorrect login attempt re-revokes their account. <!--PE-16430-->

## Performance issues with the `unpin-from-all` endpoint

As number of groups increases, response time can increase significantly with the `unpin-from-all` endpoint. <!--PE-14172-->

## Token generation error for new external directory users

If a remote user who is part of an external directory group in RBAC attempts to generate an authentication token with a specific expiry prior to logging into the PE console for the first time, an error message will be returned. New remote users should log into the PE console prior to attempting to generate authentication tokens. <!--PE-12029-->

##  Agent time skew results in incorrect error message

If a PE agent attempts to check in with the master but the agent has significant time skew, an incorrect error message is returned. This message masks the real cause of the problem, which is the time skew. The error message says: `400 Error: "Attempt to assign to a reserved variable name: 'trusted' on node"`. <!--PE-6936-->

