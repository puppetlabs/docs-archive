---
layout: default
title: "PE console and console services known issues"
canonical: "/pe/latest/release_notes_known_issues_console.html"
---

This page lists known issues for the PE console and console services in Puppet Enterprise 2016.1.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).

## **New:** Node graph display errors for names containing non-ASCII characters  

The node graph rendering tool reads both individual non-ASCII characters and groups of non-ASCII characters as a single underscore. As a result, multiple graph elements might receive the same name, which are then conflated and incorrectly mapped. <!--PE-17096-->

## **New:** The Run Puppet option for individual nodes isn't available in large installations

In installations with large numbers of nodes, the **Run Puppet** button doesn't appear on the node detail pages. You can still run Puppet on individual nodes using the command line. <!--PE-16727-->

## **New:** Underscores in node names result in error

When loading the node detail page for a node with an underscore in its name, an error appears stating, "Error retrieving node agent list: Server Error." Additionally, the option to run Puppet doesn't appear, but you can still run Puppet on the node using the command line. <!--PE-14894-->

## **New:** Count of failed login attempts does not reset after reinstating a user account

In cases where a user's account is revoked due to too many incorrect login attempts, and the account is reinstated via API by updating the `is_revoked` flag, the `failed_login_count` does not correctly reset, so the user's next incorrect login attempt re-revokes their account. <!--PE-16430-->

## **New:** Directory service setting overwritten on upgrades from 2015.3.x

When upgrading from the 2015.3 series, the `disable_ldap_matching_rule_in_chain` directory service setting reverts to its default (`false`) setting. Follow the [instructions in the RBAC API docs](./rbac_dsref_v1.html#note-matching-rule-in-chain) to change this setting to `true`. 
<!--PE-16354-->

## Response from the `unpin-from-all` endpoint slows as number of groups increase

As number of groups increases, response time can increase significantly with the `unpin-from-all` endpoint. <!--PE-14172-->

## New external directory users receive a token generation error

If a remote user who is part of an external directory group in RBAC attempts to generate an authentication token with a specific expiry prior to logging into the PE console for the first time, an error message will be returned. New remote users should log into the PE console prior to attempting to generate authentication tokens. <!--PE-12029-->

##  Agent time skew results in incorrect error message

If a PE agent attempts to check in with the master but the agent has significant time skew, an incorrect error message is returned. This message masks the real cause of the problem, which is the time skew. The error message says: `400 Error: "Attempt to assign to a reserved variable name: 'trusted' on node"`. <!--PE-6936-->

