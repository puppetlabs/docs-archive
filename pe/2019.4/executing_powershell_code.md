# Executing PowerShell code

Some Windows maintenance tasks require the use of Windows Management Instrumentation \(WMI\), and PowerShell is the most useful way to access WMI methods. Puppet has a special module that can be used to execute arbitrary PowerShell code.

A common Windows maintenance tasks is to disable Windows drive indexing, because it can negatively impact disk performance on servers.

1.  To disable drive indexing:

    ```
    $drive = 'C:'
    
    exec { 'disable-c-indexing':
       provider  => powershell,
       command   => "\$wmi_volume = Get-WmiObject -Class Win32_Volume -Filter 'DriveLetter=\"${drive}\"'; if (\$wmi_volume.IndexingEnabled -ne \$True) { return }; \$wmi_volume | Set-WmiInstance -Arguments @{IndexingEnabled = \$False}",
       unless    => "if ((Get-WmiObject -Class Win32_Volume -Filter 'DriveLetter=\"${drive}\"').IndexingEnabled) { exit 1 }",
    }
    ```


You can see the results in your object editor window:

![Object editor window showing that IndexingEnabled is set to FALSE.](windows_maintain.png)

Using the Windows built-in WBEMTest tool, running this manifest sets `IndexingEnabled` to `FALSE`, which is the desired behavior.

This `exec` sets a few important attributes:

-   The provider is configured to use PowerShell \(which relies on the module\).

-   The command contains inline PowerShell, and as such, must be escaped with PowerShell variables preceded with `$` must be escaped as `\$`.

-   The `unless` attribute is set to ensure that Puppet behaves idempotently, a key aspect of using Puppet to manage resources. If the resource is already in the desired state, Puppet does not modify the resource state. 


**Parent topic:**[Managing Windows configurations](managing_windows_configurations.md)

