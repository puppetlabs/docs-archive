# Copy an existing package and make it internal \(repackaging packages\)

To make the existing package local, use these steps.

## About this task

Chocolatey's community feed has quite a few packages, but they are geared towards community and use the internet for downloading from official distribution sites. However, they are attractive as they have everything necessary to install a piece of software on your machine. Through the repackaging process, by which you take a community package and bring all of the bits internal or embed them into the package, you can completely internalize a package to host on an internal Chocolatey/NuGet repository. This gives you complete control over a package and removes the aforementioned production trust and control issues.

## Procedure

1.  Download the Notepad++ package from Chocolatey's community feed by going to the package page and clicking the download link.

2.  Rename the downloaded file to end with `.zip` and unpack the file as a regular archive.

3.  Delete the `_rels` and `package` folders and the `[Content_Types].xml` file. These are created during choco pack and should not be included, because they're regenerated \(and their existence leads to issues\).

    ```
    notepadplusplus.commandline.6.8.7.nupkg     
    ├───_rels   # DELETE
    ├───package # DELETE
    │   └───services
    ├───tools
    ├── [Content_Types].xml	# DELETE
    └── notepadplusplus.commandline.nuspec
    ```

4.  Open `tools\chocolateyInstall.ps1`.

    ```
    Install-ChocolateyZipPackage 'notepadplusplus.commandline' 'https://notepad-plus-plus.org/repository/6.x/6.8.7/npp.6.8.7.bin.zip' "$(Split-Path -parent $MyInvocation.MyCommand.Definition)"
    ```

5.  Download the zip file and place it in the tools folder of the package.

6.  Next, edit `chocolateyInstall.ps1` to point to this embedded file instead of reaching out to the internet \(if the size of the file is over 50MB, you might want to put it on a file share somewhere internally for better performance\).

    ```
    $toolsDir   = "$(Split-Path -parent $MyInvocation.MyCommand.Definition)"
    Install-ChocolateyZipPackage 'notepadplusplus.commandline' "$toolsDir\npp.6.8.7.bin.zip" "$toolsDir"
    ```

    The double quotes allow for string interpolation \(meaning variables get interpreted instead of taken literally\).

7.  Next, open the `*.nuspec` file to view its contents and make any necessary changes.

    ```
    <?xml version="1.0"?>
    <package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
      <metadata>
        <id>notepadplusplus.commandline</id>
        <version>6.8.7</version>
        <title>Notepad++ (Portable, CommandLine)</title>
        <authors>Don Ho</authors>
        <owners>Rob Reynolds</owners>
        <projectUrl>https://notepad-plus-plus.org/</projectUrl>
        <iconUrl>https://cdn.rawgit.com/ferventcoder/chocolatey-packages/02c21bebe5abb495a56747cbb9b4b5415c933fc0/icons/notepadplusplus.png</iconUrl>
        <requireLicenseAcceptance>false</requireLicenseAcceptance>
        <description>Notepad++ is a ... </description>
        <summary>Notepad++ is a free (as in "free speech" and also as in "free beer") source code editor and Notepad replacement that supports several languages. </summary>
        <tags>notepad notepadplusplus notepad-plus-plus</tags>
      </metadata>
    </package>
    ```

    Some organizations change the version field to denote that this is an edited internal package, for example changing 6.8.7 to 6.8.7.20151202. For now, this is not necessary.

8.  Now you can navigate via the command prompt to the folder with the .nuspec file \(from a Windows machine unless you’ve installed Mono and built `choco.exe` from source\) and use `choco pack`. You can also be more specific and run `choco pack <FILE PATH>\notepadplusplus.commandline.nuspec`. The output should be similar to below.

    ```
    Attempting to build package from 'notepadplusplus.commandline.nuspec'.
    Successfully created package 'notepadplusplus.commandline.6.8.7.nupkg'
    ```

    Normally you test on a system to ensure that the package you just built is good prior to pushing the package \(just the `*.nupkg`\) to your internal repository. This can be done by using `choco.exe` on a test system to install \(`choco install notepadplusplus.commandline -source %cd%` - change `%cd%` to `$pwd` in PowerShell.exe\) and uninstall \(`choco uninstall notepadplusplus.commandline`\). Another method of testing is to run the manifest pointed to a local source folder, which is what you are going to do.

9.  Create `c:\packages` and copy the resulting package file \(`notepadplusplus.commandline.6.8.7.nupkg`\) into it.

    This won’t actually install on this system since you just installed the same version from Chocolatey’s community feed. So you need to remove the existing package first. To remove it, edit your `chocolatey.pp` to set the package to absent.

    ```
    package {'notepadplusplus.commandline':
      ensure   => absent,
      provider => chocolatey,
    }
    ```

10. Validate the manifest with `puppet parser validate path\to\chocolatey.pp`. Apply the manifest to ensure the change `puppet apply c:\path\to\chocolatey.pp`.

    You can validate that the package has been removed by checking for it in the package install location or by using `choco list -lo`.

11. Update the manifest \(`chocolatey.pp`\) to use the custom package.

    ```
    package {'notepadplusplus.commandline':
      ensure   => latest,
     provider => chocolatey,
     source   => 'c:\packages',
    }
    ```

12. Validate the manifest with the parser and then apply it again. You can see Puppet creating the new install in the output.

    ```
    Notice: Compiled catalog for win2012r2x64 in environment production in 0.79 seconds
    Notice: /Stage[main]/Main/Package[notepadplusplus.commandline]/ensure: created
    Notice: Applied catalog in 14.78 seconds
    ```

13. In an earlier step, you added a `*.zip` file to the package, so that you can inspect it and be sure the custom package was installed. Navigate to `C:\ProgramData\chocolatey\lib\notepadplusplus.commandline\tools` \(if you have a default install location for Chocolatey\) and see if you can find the `*.zip` file.

    You can also validate the `chocolateyInstall.ps1` by opening and viewing it to see that it is the custom file you changed.


