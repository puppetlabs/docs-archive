# Manage local groups

Local user accounts are often desirable for isolating applications requiring unique permissions. It can also be useful to manipulate existing local groups.

## Procedure

1.  To add domain users or groups not present in the Domain Administrators group to the local Administrators group, use this code:

    ```
    group { 'Administrators':
       ensure  => 'present',
       members => ['DOMAIN\\User'],
       auth_membership => false
    }
    ```


## Results

In this case, `auth_membership` is set to false to ensure that `DOMAIN\User` is present in the Administrators group, but that other accounts that might be present in Administrators are not removed.

Note that the `groups` attribute of `user` and the `members` attribute of `group` might both accept SID values, like the well-known SID for Administrators, S-1-5-32-544.

