---
author: Jorie Tappa <jorie@puppet.com\>
---

# Ensure a Windows service is running

There are often services that you always want running in your infrastructure.

1.  To have Puppet ensure that a service is running, use the following code:

    ```
    service { '<service name>':
           ensure => 'running'
        }
    ```


For example, the following manifest code ensures the  Windows Time service is running:

```
service { 'w32time':
  ensure => 'running'
}
```

**Parent topic:**[Manage Windows services](managing_windows_services.md)

