# View file permissions with ACL

ACL is a custom type and provider, so you can use `puppet resource` to look at existing file and folder permissions.

For some types, you can use the command `puppet resource <TYPE NAME>` to get all instances of that type. However, there could be thousands of ACLs on a Windows system, so it's best to specify the folder you want to review the types in. Here, check `c:\Users` to see what permissions it contains.

1.  In the command prompt, enter `puppet resource acl c:\Users`

    ```
    acl { 'c:\Users':
      inherit_parent_permissions => 'false',
      permissions                => [
       {identity => 'SYSTEM', rights=> ['full']},
       {identity => 'Administrators', rights => ['full']},
       {identity => 'Users', rights => ['read', 'execute'], affects => 'self_only'},
       {identity => 'Users', rights => ['read', 'execute'], affects => 'children_only'},
       {identity => 'Everyone', rights => ['read', 'execute'], affects => 'self_only'},
       {identity => 'Everyone', rights => ['read', 'execute'], affects => 'children_only'}
      ],
    }
    ```


As you can see, this particular folder does not inherit permissions from its parent folder; instead, it sets its own permissions and determines how child files and folders inherit the permissions set here.

-   `{'identity' => 'SYSTEM', 'rights'=> ['full']}` states that the “SYSTEM” user has full rights to this folder, and by default all children and grandchildren files and folders \(as these are the same defaults when creating permissions in Windows\).

-   `{'identity' => 'Users', 'rights' => ['read', 'execute'], 'affects' => 'self_only'}` gives read and execute permissions to Users but only on the current directory.

-   `{'identity' => 'Everyone', 'rights' => ['read', 'execute'], 'affects' => 'children_only'}` gives read and execute permissions to everyone, but only on subfolders and files.


**Note:** You might see what appears to be the same permission for a user/group twice \(both "Users" and "Everyone" above\), where one affects only the folder itself and the other is about children only. They are in fact different permissions.

**Parent topic:**[Manage permissions with acl](manage_permissions_with_acl.md)

