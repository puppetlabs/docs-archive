# Install Chocolatey

These steps are to install Chocolatey \(choco.exe\) itself. You use the module to ensure Chocolatey is installed.

## Procedure

1.  Create a new manifest in the chocolatey module called `chocolatey.pp` with the following contents:

    ```
    include chocolatey
    ```

2.  Validate the manifest by running `puppet parser validate c:\<FILE PATH>\chocolatey.pp` in the command prompt. If the parser returns nothing, it means validation passed.

3.  Apply the manifest by running `puppet apply c:\<FILE PATH>\chocolatey.pp`

    Your output should look similar to below:

    ```
    Notice: Compiled catalog for win2012r2x64 in environment production in 0.58 seconds
    Notice: /Stage[main]/Chocolatey::Install/Windows_env[chocolatey_PATH_env]/ensure
    : created
    Notice: /Stage[main]/Chocolatey::Install/Windows_env[chocolatey_ChocolateyInstal
    l_env]/ensure: created
    Notice: /Stage[main]/Chocolatey::Install/Exec[install_chocolatey_official]/retur
    ns: executed successfully
    Notice: /Stage[main]/Chocolatey::Install/Exec[install_chocolatey_official]: Trig
    gered 'refresh' from 2 events
    Notice: Finished catalog run in 13.22 seconds
    ```

    In a production scenario, you’re likely to have a Chocolatey.nupkg file somewhere internal. In cases like that, you can use the internal nupkg for Chocolatey installation:

    ```
    class {'chocolatey':
      chocolatey_download_url => 'https://internalurl/to/chocolatey.nupkg',
      use_7zip                => false,
      log_output              => true,
    }
    ```


