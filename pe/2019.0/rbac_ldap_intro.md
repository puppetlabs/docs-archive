# Connecting external directory services to PE

Puppet Enterprise connects to external Lightweight Directory Access Protocol \(LDAP\) directory services through its role-based access control \(RBAC\) service. This allows you to use existing users and user groups that have been set up in your external directory service.

Specifically, you can:

-   Authenticate external directory users.
-   Authorize access of external directory users based on RBAC permissions.
-   Store and retrieve the groups and group membership information that has been set up in your external directory.

    **Note:** Puppet stores local accounts and directory service integration credentials securely. Local account passwords are hashed using SHA-256 multiple times along with a 32-bit salt. Directory service lookup credentials configured for directory lookup purposes are encrypted using AES-128. Puppet does not store the directory credentials used for authenticating to Puppet. These are different from the directory service lookup credentials.


PE supports OpenLDAP and Active Directory. If you have predefined groups in your Active Directory or OpenLDAP directory, you can import these groups into the console and assign user roles to them. Users in an imported group inherit the permissions specified in assigned user roles. If new users are added to the group in the external directory, they also inherit the permissions of the role to which that group belongs.

**Note:** The connection to your external LDAP directory is read-only. If you want to make changes to remote users or user groups, you need to edit the information directly in the external directory.

## Connect to an external directory service

PE connects to the external directory service when a user logs in or when groups are imported. The supported directory services are OpenLDAP and Active Directory.

### Procedure

1.  In the console, click Access control \> External directory.

2.  Fill in the directory information.

    All fields are required, except for **Login help**, **Lookup user**, **Lookup password**, **User relative distinguished name**, and **Group relative distinguished name**.

    If you do not enter **User relative distinguished name** or **Group relative distinguished name**, RBAC searches the entire base DN for the user or group.

3.  Click **Test connection** to ensure that the connection has been established. Save your settings after you have successfully tested them.


## External directory settings

The table below provides examples of the settings used to connect to an Active Directory service and an OpenLDAP service to PE. Each setting is explained in more detail below the table.

**Important:** The settings shown in the table are examples. You need to substitute these example settings with the settings used in your directory service.

|Name|Example Active Directory settings|Example OpenLDAP settings|
|----|---------------------------------|-------------------------|
|**Directory name**|My Active Directory|My Open LDAP Directory|
|**Login help \(optional\)**|https://myweb.com/ldaploginhelp|https://myweb.com/ldaploginhelp|
|**Hostname**|myhost.delivery.exampleservice.net|myhost.delivery.exampleservice.net|
|**Port**|389 \(636 for LDAPS\)|389 \(636 for LDAPS\)|
|**Lookup user \(optional\)**|cn=queryuser,cn=Users,dc=puppetlabs,dc=com|cn=admin,dc=delivery,dc=puppetlabs,dc=net|
|**Lookup password \(optional\)**|The lookup user's password.|The lookup user's password.|
|**Connection timeout \(seconds\)**|10|10|
|**Connect using:**|SSL

|StartTLS

|
|**Validate the hostname?**|Default is yes.|Default is yes.|
|**Allow wildcards in SSL certificate?**|Default is no.|Default is no.|
|**Base distinguished name**|dc=puppetlabs,dc=com|dc=puppetlabs,dc=com|
|**User login attribute**|sAMAccountName|cn|
|**User email address**|mail|mail|
|**User full name**|displayName|displayName|
|**User relative distinguished name \(optional\)**|cn=users|ou=users|
|**Group object class**|group|groupOfUniqueNames|
|**Group membership field**|member|uniqueMember|
|**Group name attribute**|name|displayName|
|**Group lookup attribute**|cn|cn|
|**Group relative distinguished name \(optional\)**|cn=groups|ou=groups|
|**Turn off LDAP\_MATCHING\_RULE\_IN\_CHAIN?**|Default is no.|Default is no.|
|**Search nested groups?**|Default is no.|Default is no.|

### Explanation of external directory settings

**Directory name** The name that you provide here is used to refer to the external directory service anywhere it is used in the PE console. For example, when you view a remote user in the console, the name that you provide in this field is listed in the console as the source for that user. Set any name of your choice.

**Login help \(optional\)** If you supply a URL here, a "Need help logging in?" link is displayed on the login screen. The href attribute of this link is set to the URL that you provide.

**Hostname** The FQDN of the directory service to which you are connecting.

**Port** The port that PE uses to access the directory service. The port is generally 389, unless you choose to connect using SSL, in which case it is generally 636.

**Lookup user \(optional\)** The distinguished name \(DN\) of the directory service user account that PE uses to query information about users and groups in the directory server. If a username is supplied, this user must have read access for all directory entries that are to be used in the console. We recommend that this user is restricted to read-only access to the directory service.

If your LDAP server is configured to allow anonymous binding, you do not need to provide a lookup user. In this case, the RBAC service binds anonymously to your LDAP server.

**Lookup password \(optional\)** The lookup user's password.

If your LDAP server is configured to allow anonymous binding, you do not need to provide a lookup password. In this case, the RBAC service binds anonymously to your LDAP server.

**Connection timeout \(seconds\)** The number of seconds that PE attempts to connect to the directory server before timing out. Ten seconds is fine in the majority of cases. If you are experiencing timeout errors, make sure the directory service is up and reachable, and then increase the timeout if necessary.

**Connect using:**Select the security protocol you want to use to connect to the external directory: **SSL** and **StartTLS** encrypt the data transmitted. **Plain text** is not a secure connection. In addition, to ensure that the directory service is properly identified, configure the `ds-trust-chain` to point to a copy of the public key for the directory service. For more information, see [Verify directory server certificates](rbac_ldap_intro.md#).

**Validate the hostname?** Select **Yes** to verify that the Directory Services hostname used to connect to the LDAP server matches the hostname on the SSL certificate. This option is not available when you choose to connect to the external directory using plain text.

**Allow wildcards in SLL certificate?** Select **Yes** to allow a connection to a Directory Services server with a SSL certificates that use a wildcard \(\*\) specification. This option is not available when you choose to connect to the external directory using plain text.

**Base distinguished name** When PE constructs queries to your external directory \(for example to look up user groups or users\), the queries consist of the relative distinguished name \(RDN\) \(optional\) + the base distinguished name \(DN\), and are then filtered by lookup/login attributes. For example, if PE wants to authenticate a user named Bob who has the RDN `ou=bob,ou=users`, it sends a query in which the RDN is concatenated with the DN specified in this field \(for example, `dc=puppetlabs,dc=com`\). This gives a search base of `ou=bob,ou=users,dc=puppetlabs,dc=com`.

The base DN that you provide in this field specifies where in the directory service tree to search for groups and users. It is the part of the DN that all users and groups that you want to use have in common. It is commonly the root DN \(example `dc=example,dc=com`\) but in the following example of a directory service entry, you could set the base DN to `ou=Puppet,dc=example,dc=com` since both the group and the user are also under the organizational unit `ou=Puppet`.

**Example directory service entry**

```
# A user named Harold
dn: cn=harold,ou=Users,ou=Puppet,dc=example,dc=com
objectClass: organizationalPerson
cn: harold
displayName: Harold J.
mail: harold@example.com
memberOf: inspectors
sAMAccountName: harold11

# A group Harold is in
dn: cn=inspectors,ou=Groups,ou=Puppet,dc=example,dc=com
objectClass: group
cn: inspectors
displayName: The Inspectors
member: harold
```

**User login attribute** This is the directory attribute that the user uses to log in to PE. For example, if you specify `sAMAccountName` as the user login attribute, Harold logs in with the username "harold11" because `sAMAccountName=harold11` in the example directory service entry provided above.

The value provided by the user login attribute must be unique among all entries under the User RDN + Base DN search base you’ve set up.

For example, say you’ve selected the following settings:

```
base DN = dc=example,dc=com
user RDN = null
user login attribute = cn
```

When Harold tries to log in, the console searches the external directory for any entries under `dc=example,dc=com` that have the attribute/value pair `cn=harold`. \(This attribute/value pair does not need to be contained within the DN\). However, if there is another user named Harold who has the DN `cn=harold,ou=OtherUsers,dc=example,dc=com`, two results are returned and the login does not succeed because the console does not know which entry to use. Resolve this issue by either narrowing your search base such that only one of the entries can be found, or using a value for login attribute that you know to be unique. This makes `sAMAccountName` a good choice if you’re using Active Directory, as it must be unique across the entire directory.

**User email address** The directory attribute to use when displaying the user's email address in PE.

**User full name** The directory attribute to use when displaying the user's full name in PE.

**User relative distinguished name \(optional\)** The user RDN that you set here is concatenated with the base DN to form the search base when looking up a user. For example, if you specify `ou=users` for the user RDN, and your base DN setting is `ou=Puppet,dc=example,dc=com`, PE finds users that have `ou=users,ou=Puppet,dc=example,dc=com` in their DN.

This setting is optional. If you choose not to set it, PE searches for the user in the base DN \(example: `ou=Puppet,dc=example,dc=com`\). Setting a user RDN is helpful in the following situations:

-   When you experience long wait times for operations that contact the directory service \(either when logging in or importing a group for the first time\). Specifying a user RDN reduces the number of entries that are searched.
-   When you have more than one entry under your base DN with the same login value.

**Tip:** It is not currently possible to specify multiple user RDNs. If you want to filter RDNs when constructing your query, we suggest creating a new lookup user who only has read access for the users and groups you want to use in PE.

**Group object class** The name of an object class that all groups have.

**Group membership field** Tells PE how to find which users belong to which groups. This is the name of the attribute in the external directory groups that indicates who the group members are.

**Group name attribute** The attribute that stores the display name for groups. This is used for display purposes only.

**Group lookup attribute** The value used to import groups into PE. Given the example directory service entry provided above, the group lookup attribute would be `cn`. When specifying the Inspectors group in the console to import it, provide the name `inspectors`.

The value for this attribute must be unique under your search base. If you have users with the same login as the lookup of a group that you want to use, you can narrow the search base, use a value for the lookup attribute that you know to be unique, or specify the **Group object class** that all of your groups have in common but your users do not.

**Tip:** If you have a large number of nested groups in your group hierarchy, or you experience slowness when logging in with RBAC, we recommend disabling nested group search unless you need it for your authorization schema to work.

**Group relative distinguished name \(optional\)** The group RDN that you set here is concatenated with the base DN to form the search base when looking up a group. For example, if you specify `ou=groups` for the group RDN, and your base DN setting is `ou=Puppet,dc=example,dc=com`, PE finds groups that have `ou=groups,ou=Puppet,dc=example,dc=com` in their DN.

This setting is optional. If you choose not to set it, PE searches for the group in the base DN \(example: `ou=Puppet,dc=example,dc=com`\). Setting a group RDN is helpful in the following situations:

-   When you experience long wait times for operations that contact the directory service \(either when logging in or importing a group for the first time\). Specifying a group RDN reduces the number of entries that are searched.
-   When you have more than one entry under your base DN with the same lookup value.

**Tip:** It is not currently possible to specify multiple group RDNs. If you want to filter RDNs when constructing your query, create a new lookup user who only has read access for the users and groups you plan to use in PE.

**Note:** At present, PE supports only a single Base DN. Use of multiple user RDNs or group RDNs is not supported.

**Turn off LDAP\_MATCHING\_RULE\_IN\_CHAIN?** Select **Yes** to turn off the LDAP matching rule that looks up the chain of ancestry for an object until it finds a match. For organizations with a large number of group memberships, matching rule in chain can slow performance.

**Search nested groups?** Select **Yes** to search for groups that are members of an external directory group. For organizations with a large number of nested group memberships, searching nested groups can slow performance.

**Related information**  


[PUT /ds](rbac_api_v1_directory.md#)

## Verify directory server certificates

To ensure that RBAC isn't being subjected to a Man-in-the Middle \(MITM\) attack, verify the directory server's certificate.

### About this task

When you select SSL or StartTLS as the security protocol to use for communications between PE and your directory server, the connection to the directory is encrypted. To ensure that the directory service is properly identified, configure the `ds-trust-chain` to point to a copy of the public key for the directory service.

The RBAC service verifies directory server certificates using a trust store file, in Java Key Store \(JKS\), PEM, or PKCS12 format, that contains the chain of trust for the directory server's certificate. This file needs to exist on disk in a location that is readable by the user running the RBAC service.

To turn on verification:

### Procedure

1.  In the console, click Classification.

2.  Open the **PE Infrastructure** node group and select the **PE Console** node group.

3.  Click **Configuration**. Locate the **puppet\_enterprise::profile::console** class.

4.  In the **Parameter** field, select **rbac\_ds\_trust\_chain**.

5.  In the **Value** field, set the absolute path to the trust store file.

6.  Click **Add parameter**, and commit changes.

7.  To make the change take effect, run Puppet. Running Puppet restarts pe-console-services.


### Results

After this value is set, the directory server's certificate is verified whenever RBAC is configured to connect to the directory server using SSL or StartTLS.

