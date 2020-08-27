---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configure LDAP

Continuous Delivery for Puppet Enterprise \(PE\) supports use of the Lightweight Directory Access Protocol \(LDAP\) for managing user authentication. Once an LDAP configuration is in place, use group mapping to associate your existing LDAP groups with role-based access control \(RBAC\) groups in Continuous Delivery for PE.

For organizational or failover protection purposes, you can add multiple LDAP configurations, each specifying a separate LDAP server, to your Continuous Delivery for PE instance. Continuous Delivery for PE uses the LDAP configurations you set up to search your LDAP users in a specified order. Once a user is found, the search ends and that LDAP configuration is used to perform the login operation.

## Create a new LDAP configuration

Add an LDAP configuration to Continuous Delivery for PE by providing key information on the mapping of user and group attributes in your LDAP server implementation.

### Procedure

1.  Log into the root console by adding `/root/login` to the end of the base URL of the Continuous Delivery for PE web UI and signing in as the root user.

2.  Click **Settings**, then click **Single Sign On**.

3.  Select **LDAP** and click **+ Add LDAP Configuration**.

4.  In the New LDAP Configuration pane, enter the requested information as per the instructions below.

    -   **Name**

        A friendly identifier for this LDAP configuration. This name cannot be changed, so select it carefully.

    -   **Endpoint URL**

        The endpoint URL of the LDAP server, including the LDAP scheme, hostname, and port. If these aren’t included, the default scheme is `ldaps` and the default port is 636.

    -   **Bind DN**

        The distinguished name of the LDAP account that Continuous Delivery for PE will bind as when performing LDAP operations. An admin account or a service account created specifically for Continuous Delivery for PE is generally used here. If you choose to create a new account, ensure that it has permission to search for users and groups.

    -   **Bind DN password**

        The password associated with the bind DN account.

        **Note:** You must enter this password each time the LDAP configuration is updated.

    -   **User base DN**

        The LDAP base DN that informs Continuous Delivery for PE where users are located in the directory. The more specific the DN, the better your LDAP search performance will be.

    -   **User attribute**

        Which LDAP user attribute to use to map users. Since a user's email address is required to log into Continuous Delivery for PE, use a user attribute with an email address for a value \(most commonly `mail`\). If your LDAP users have duplicate emails, they will need to be changed.

    -   **User base filter**

        A filter that Continuous Delivery for PE can use to restrict user search results. This is useful in edge cases where certain users should be included or excluded.

    -   **Group base DN**

        The LDAP base DN that informs Continuous Delivery for PE where groups are located in the directory. The more specific the DN, the better your LDAP search performance will be.

        **Note:** If users and groups are stored in the same location, the value in this field will be the same as the value in the **User Attribute** field.

    -   **Group user attribute**

        The user attribute that group entries use to identify users. In most cases the value of this option is `dn`.

    -   **Group member attribute**

        The group attribute that maps to a group member. In most cases the value of this option is either `member` or `uniqueMember`.

    -   **Group name attribute**

        The group attribute that identifies the group name. In most cases the value of this option is `cn`.

    -   **Group base filter**

        A filter that Continuous Delivery for PE can use to restrict group search results. This is useful in edge cases where certain groups should be included or excluded.

    -   **User object class**

        Specifies the value of the `objectClass` attribute that allows Continuous Delivery for PE to query user entries. In most cases the value of this option is `user` or `person`.

    -   **Group object class**

        Specifies the value of the `objectClass` attribute that allows Continuous Delivery for PE to query group entries. In most cases the value of this option is `group` or `groupOfUniqueNames`.

    -   **User member attribute**

        Specifies the user attribute that can be used to identify the membership of a group. If present, the value of this option is generally `memberOf`.

5.  Select the **Priority** number for this LDAP configuration. This number indicates the order in which your LDAP configurations are used to search for users during login and when synchronizing groups.

6.  Enter the trusted server's CA certificate. If the LDAP server uses a certificate signed by a trusted CA, you do not need to enter a CA certificate here.

7.  Set the toggle to enable recursive LDAP queries for nested groups. This option is available only if your LDAP server's implementation supports the [ExtensibleMatch search filter](https://ldapwiki.com/wiki/ExtensibleMatch).

8.  Set the toggle to **Enable LDAP**, then click **Run Configuration Test** to check the connection between Continuous Delivery for PE and the LDAP server.

    **Note:** This configuration test checks to see if the LDAP server can be reached; it does not test the other configuration options.

9.  Click **Save Configuration**. Your new LDAP configuration is now shown on the **Single Sign On** settings page, where you have the option to edit or delete the configuration.

    **Important:** Once you enable an LDAP configuration, Continuous Delivery for PE will automatically disable all local Continuous Delivery for PE accounts other than the root account, and will attempt to use LDAP authentication. If your LDAP authentication fails, sign in as the root user by adding `/root/login` to the end of the base URL of the Continuous Delivery for PE web UI and adjust your settings.


## Create an LDAP group map

Once you add an LDAP configuration to Continuous Delivery for PE, use a group map to map your existing LDAP groups to Continuous Delivery for PE RBAC groups. This makes it possible to mirror group membership defined in LDAP to groups in Continuous Delivery for PE.

### Before you begin

Add at least one LDAP configuration to your Continuous Delivery for PE instance.

### Procedure

1.  Log into the root console by adding `/root/login` to the end of the base URL of the web UI and signing in as the root user.

2.  Click **Settings**, then click **Single Sign On**.

3.  Click **LDAP**, then click **Manage Groups**. Click **+ Add LDAP Group Mapping**

4.  Select the LDAP configuration to associate the group mapping with.

5.  From the list of available LDAP groups, select the group to be used to perform the mapping.

6.  Select a Continuous Delivery for PE account that is associated with the RBAC group you wish to map to the selected LDAP group.

7.  From the list of available Continuous Delivery for PE RBAC groups, select the RBAC group you wish to map to the selected LDAP group.

8.  Click **Add Group Mapping**.

    Once a group map is set up, Continuous Delivery for PE synchronizes with your LDAP groups based on the mapping you create.


