# Configure an app to use a different account

You might not always want to use the default user for an application, you can use Puppet to create users for other applications, like ASP.NET.

## Procedure

1.  To configure ASP.NET apps to use accounts other than the default `Network Service`, create a user and exec resource: 

    ```
    user { 'aspnet_banking_app':
       ensure          => present,
       managehome      => true,
       comment         => 'ASP.NET Service account for Banking application',
       password        => 'banking_app_password',
       groups          => ['IIS_IUSRS', 'Users'],
       auth_membership => 'minimum',
       notify          => Exec['regiis_aspnet_banking_app']
    }
    
    exec { 'regiis_aspnet_banking_app':
       path        => 'c:\\windows\\Microsoft.NET\\Framework\\v4.0.30319',
       command     => 'aspnet_regiis.exe -ga aspnet_banking_app',
       refreshonly => true
    }
    ```


## Results

In this example, the user is created in the appropriate groups, and the ASP.NET IIS registration command is run after the user is created to ensure file permissions are correct.

![The group properties window showing that the user in the previous example has been created.](windows_iis_users.png)

In the user resource, there are a few important details to note:

-   `managehome` is set to create the user's home directory on disk.

-   `auth_membership` is set to minimum, meaning that Puppet makes sure the `aspnet_banking_app` user is a part of the `IIS_IUSRS` and `Users` group, but doesn't remove the user from any other groups it might be a part of.

-   `notify` is set on the user, and `refreshonly` is set on the `exec`. This tells Puppet to run `aspnet_regiis.exe` only when the `aspnet_banking_app` is created or changed.


