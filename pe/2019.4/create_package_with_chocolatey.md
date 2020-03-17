# Create a package with `chocolatey`

Creating your own packages is, for some system administrators, surprisingly simple compared to other packaging standards.

Ensure you have at least Chocolatey CLI \(choco.exe\) version `0.9.9.11` or newer for this next part.

1.  From the command prompt, enter `choco new -h` to see a help menu of what the available options are.

2.  Next, use `choco new vagrant` to create a package named 'vagrant'. The output should be similar to the following:

    ```
    Creating a new package specification at C:\temppackages\vagrant
       Generating template to a file
        at 'C:\temppackages\vagrant\vagrant.nuspec'
       Generating template to a file
        at 'C:\temppackages\vagrant\tools\chocolateyinstall.ps1'
       Generating template to a file
        at 'C:\temppackages\vagrant\tools\chocolateyuninstall.ps1'
       Generating template to a file
        at 'C:\temppackages\vagrant\tools\ReadMe.md'
       Successfully generated vagrant package specification files
        at 'C:\temppackages\vagrant'
    
    ```

    It comes with some files already templated for you to fill out \(you can also create your own custom templates for later use\).

3.  Open `vagrant.nuspec`, and edit it to look like this:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <package xmlns="http://schemas.microsoft.com/packaging/2015/06/nuspec.xsd">
      <metadata>
        <id>vagrant</id>
        <title>Vagrant (Install)</title>
        <version>1.8.4</version>
        <authors>HashiCorp</authors>
        <owners>my company</owners>
        <description>Vagrant - Development environments made easy.</description>
      </metadata>
      <files>
        <file src="tools\**" target="tools" />
      </files>
    </package>
    
    ```

    Unless you are sharing with the world, you don’t need most of what is in the nuspec template file, so only required items are included above. Match the version of the package in this nuspec file to the version of the underlying software as closely as possible. In this example, Vagrant 1.8.4 is being packaged. 

4.  Open `chocolateyInstall.ps1` and edit it to look like the following:

    ```
    $ErrorActionPreference = 'Stop';
    
    $packageName= 'vagrant'
    $toolsDir   = "$(Split-Path -parent $MyInvocation.MyCommand.Definition)"
    $fileLocation = Join-Path $toolsDir 'vagrant_1.8.4.msi'
    
    $packageArgs = @{
      packageName   = $packageName
      fileType      = 'msi'
      file         = $fileLocation
    
      silentArgs    = "/qn /norestart"
      validExitCodes= @(0, 3010, 1641)
    }
    
    Install-ChocolateyInstallPackage @packageArgs
    ```

    **Note:** The above is [Install-ChocolateyINSTALLPackage](https://github.com/chocolatey/choco/wiki/HelpersInstallChocolateyInstallPackage), not to be confused with [Install-ChocolateyPackage](https://github.com/chocolatey/choco/wiki/HelpersInstallChocolateyPackage). The names are very close to each other, however the latter also downloads software from a URI \(URL, ftp, file\) which is not necessary for this example.

5.  Delete the `ReadMe.md` and `chocolateyUninstall.ps1` files. [Download Vagrant](https://www.vagrantup.com/downloads.html) and move it to the tools folder of the package.

    **Note:** Normally if a package is over 100MB, it is recommended to move the software installer/archive to a share drive and point to it instead. For this example, just bundle it as is.

6.  Now pack it up by using `choco pack`. Copy the new `vagrant.1.8.4.nupkg` file to `c:\packages`.

7.  Open the manifest, and add the new package you just created. Your `chocolatey.pp` file should look like the below.

    ```
    package {'vagrant':
      ensure   => installed,
      provider => chocolatey,
      source   => 'c:\packages',
    }
    ```

8.  Save the file and make sure to validate with the Puppet parser.

9.  Use `puppet apply <FILE PATH>\chocolatey.pp` to run the manifest.

10. Open **Control Panel**, **Programs and Features** and take a look.


![A screenshot of the Programs and Features window, with Vagrant listed as an installed program.](choco_vagrant_installed.png)

Vagrant is installed!

**Parent topic:**[Create, install and repackage packages with the chocolatey module](create_install_repackage_with_chocolatey.md)

