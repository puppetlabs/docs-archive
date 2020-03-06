# View `registry` keys and values with `puppet resource`

`puppetlabs-registry` is a custom type and provider, so you can use `puppet resource` to look at existing registry settings.

## About this task

It is also somewhat limited, like the `acl` module in that it is restricted to only what is specified.

## Procedure

1.  In your command prompt, run: `puppet resource registry_key 'HKLM\Software\Microsoft\Windows'`

    ![Screenshot of the command with a result showing that the specified registry key is present.](cli_resource_registry.png)

    Not that interesting, but now take a look at a registry value.

2.  Enter `puppet resource registry_value 'HKLM\SYSTEM\CurrentControlSet\Services\BITS\DisplayName'`

    ```
    registry_value { 'HKLM\SYSTEM\CurrentControlSet\Services\BITS\DisplayName':
      ensure => 'present',
      data   => ['Background Intelligent Transfer Service'],
      type   => 'string',
    }
    ```

    That’s a bit more interesting than a registry key.


## Results

Keys are like file paths \(directories\) and values are like files that can have data and be of different types.

