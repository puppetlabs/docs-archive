# Stop a Windows service

Some services can impair performance, or might need to be stopped for regular maintenance.

1.  To disable a service, use the code:

    ```
    service { '<service name>':
       ensure => 'stopped',
       enable => 'false'
    }
    ```


For example, this disables the disk defragmentation service, which can negatively impact service performance.

```
 service { 'defragsvc':
  ensure => 'stopped',
  enable => 'false'
}
```

**Parent topic:**[Manage Windows services](managing_windows_services.md)

