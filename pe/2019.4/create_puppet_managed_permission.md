# Create a Puppet managed permission

1.  Run this code to create your first Puppet managed permission. Then, save it as `perms.pp`

    ```
    file{'c:/tempperms':
     ensure => directory,
    }
    
    # By default, the acl creates an implicit relationship to any
    # file resources it finds that match the location.
    acl {'c:/tempperms':
     permissions => [
       {identity => 'Administrators', rights => ['full']},
       {identity => 'Users', rights => ['read','execute']}
     ],
    }
    ```

2.  To validate your manifest, in the command prompt, run `puppet parser validate c:\<FILE PATH>\perms.pp`. If the parser returns nothing, it means validation passed.

3.  To apply the manifest, type `puppet apply c:\<FILE PATH>\perms.pp`

    Your output should look similar to:

    ```
    Notice: Compiled catalog for win2012r2x64 in environment production in 0.12 seconds
    Notice: /Stage[main]/Main/File[c:/tempperms]/ensure: created
    Notice: /Stage[main]/Main/Acl[c:/tempperms]/permissions: permissions changed [
    ] to [
     { identity => 'BUILTIN\Administrators', rights => ["full"] },
     { identity => 'BUILTIN\Users', rights => ["read", "execute"] }
    ]
    Notice: Applied catalog in 0.05 seconds
    ```

4.  Review the permissions in your Windows UI. In Windows Explorer, right-click **tempperms** and click **Properties**. Then, click the **Security** tab. It should appear similar to the image below.

    ![The tempperms window with the security tab highlighted.](security_properties.png)

5.  Optional: It might appear that you have more permissions than you were hoping for here. This is because by default Windows inherits parent permissions. In this case, you might not want to do that. Adjust the acl resource to not inherit parent permissions by changing the `perms.pp` file to look like the below by adding `inherit_parent_permissions => false`.

    ```
    acl {'c:/tempperms':
      inherit_parent_permissions => false,
      permissions                => [
        {identity => 'Administrators', rights => ['full']},
        {identity => 'Users', rights => ['read','execute']}
      ],
    }
    ```

6.  Save the file, and return the command prompt to run `puppet parser validate c:\<FILE PATH>\perms.pp` again.

7.  When it validates, run `puppet apply c:\<FILE PATH>\perms.pp`

    You should get output similar to the following:

    ```
    C:\>puppet apply c:\puppet_code\perms.pp
    Notice: Compiled catalog for win2012r2x64 in environment production in 0.08 seconds
    Notice: /Stage[main]/Main/Acl[c:/tempperms]/inherit_parent_permissions: inherit_
    parent_permissions changed 'true' to 'false'
    Notice: Applied catalog in 0.02 seconds
    ```

8.  To check the permissions again, enter `icacls c:\tempperms` in the command prompt. The command, `icacls`, is specifically for displaying and modifying ACLs. The output should be similar to the following:

    ```
    C:\>icacls c:\tempperms
    c:\tempperms BUILTIN\Administrators:(OI)(CI)(F)
                 BUILTIN\Users:(OI)(CI)(RX)
                 NT AUTHORITY\SYSTEM:(OI)(CI)(F)
                 BUILTIN\Users:(CI)(AD)
                 CREATOR OWNER:(OI)(CI)(IO)(F)
    Successfully processed 1 files; Failed processing 0 files
    ```

    The output shows each permission, followed by a list of specific rights in parentheses. This output shows there are more permissions than you specified in `perms.pp`. Puppet manages permissions next to unmanaged or existing permissions. In the case of removing inheritance, by default Windows copies those existing inherited permissions \(or Access Control Entries, ACEs\) over to the existing ACL so you have some more permissions that you might not want.

9.  Remove the extra permissions, so that only the permissions you’ve specified are on the folder. To do this, in your perms.pp set purge =\> true as follows:

    ```
    acl {'c:/tempperms':
      inherit_parent_permissions => false,
      purge                      => true,
      permissions                => [
        {identity => 'Administrators', rights => ['full']},
        {identity => 'Users', rights => ['read','execute']}
      ],
    }
    ```

10. Run the parser command as you have before. If it still returns no errors, then you can apply the change.

11. To apply the change, run `puppet apply c:\<FILE PATH>\perms.pp`. The output should be similar to below:

    ```
    C:\>puppet apply c:\puppet_code\perms.pp
    Notice: Compiled catalog for win2012r2x64 in environment production in 0.08 seco
    nds
    Notice: /Stage[main]/Main/Acl[c:/tempperms]/permissions: permissions changed [
    { identity => 'BUILTIN\Administrators', rights => ["full"] },
    { identity => 'BUILTIN\Users', rights => ["read", "execute"] },
    { identity => 'NT AUTHORITY\SYSTEM', rights => ["full"] },
    { identity => 'BUILTIN\Users', rights => ["mask_specific"], mask => '4', child_types => 'containers' },
    { identity => 'CREATOR OWNER', rights => ["full"], affects => 'children_only' }
    ] to [
    { identity => 'BUILTIN\Administrators', rights => ["full"] },
    { identity => 'BUILTIN\Users', rights => ["read", "execute"] }
    ]
    Notice: Applied catalog in 0.05 seconds
    ```

    Puppet outputs a notice as it is removing each of the permissions.

12. Take a look at the output of `icacls` again with `icacls c:\tempperms`

    ```
    c:\>icacls c:\tempperms
    c:\tempperms BUILTIN\Administrators:(OI)(CI)(F)
                BUILTIN\Users:(OI)(CI)(RX)
    Successfully processed 1 files; Failed processing 0 files
    ```


Now the permissions have been set up for this directory. You can get into more advanced permission scenarios if you read the usage scenarios on this module’s Forge page.

**Parent topic:**[Manage permissions with acl](manage_permissions_with_acl.md)

