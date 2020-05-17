# Install a package with `chocolatey`

Normally, when installing packages you copy them locally first, make any required changes to bring everything they download to an internal location, repackage the package with the edits, and build your own packages to host on your internal package repository \(feed\). For this exercise, however, you directly install a portable Notepad++ from Chocolatey's community feed. The Notepad++ CommandLine package is portable and shouldn't greatly affect an existing system.

## Procedure

1.  Update the manifest chocolatey.pp with the following contents:

    ```
    package {'notepadplusplus.commandline':
      ensure   => installed,
      provider => chocolatey,
    }
    ```

2.  Validate the manifest by running `puppet parser validate c:\<FILE PATH>\chocolatey.pp` in the command prompt. If the parser returns nothing, it means validation passed.

3.  Now, apply the manifest with `puppet apply c:\<FILE PATH>\chocolatey.pp`. Your output should look similar to below.

    ```
    Notice: Compiled catalog for win2012r2x64 in environment production in 0.75 seconds
    Notice: /Stage[main]/Main/Package[notepadplusplus.commandline]/ensure: created
    Notice: Applied catalog in 15.51 seconds
    ```


## Results

If you want to use this package for a production scenario, you need an internal custom feed. This is simple to set up with the `chocolatey_server` module. You could also use Sonatype Nexus, Artifactory, or a CIFS share if you want to host packages with a non-Windows option, or you can use anything on Windows that exposes a NuGet OData feed \(Nuget is the packaging infrastructure that Chocolatey uses\). See the [How To Host Feed page of the `chocolatey` wiki](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) for more in-depth information. You could also store packages on your master and use a file resource to verify they are in a specific local directory prior to ensuring the packages.

## Example

The following example ensures that Chocolatey, the Chocolatey Simple Server \(an internal Chocolatey package repository\), and some packages are installed. It requires the additional [chocolatey/chocolatey\_server module](https://forge.puppetlabs.com/chocolatey/chocolatey_server).

In `c:\<FILE PATH>\packages` you must have packages for [Chocolatey](https://chocolatey.org/packages/chocolatey), [Chocolatey.Server](https://chocolatey.org/packages/chocolatey.server), [RoundhousE](https://chocolatey.org/packages/roundhouse), [Launchy](https://chocolatey.org/packages/launchy), and [Git](https://chocolatey.org/packages/git), as well as any of their dependencies for this to work.

```
case $operatingsystem {
  'windows':    {
    Package {
      provider => chocolatey,
      source   => 'C:\packages',
    }
  }
}

# include chocolatey
class {'chocolatey':
  chocolatey_download_url => 'file:///C:/packages/chocolatey.0.9.9.11.nupkg',
  use_7zip                => false,
  log_output              => true,
}

# This contains the bits to install the custom server.
# include chocolatey_server
class {'chocolatey_server':
  server_package_source => 'C:/packages',
}

package {'roundhouse':
  ensure   => '0.8.5.0',
}


package {'launchy':
  ensure          => installed,
  install_options => ['-override', '-installArgs','"', '/VERYSILENT','/NORESTART','"'],
}

package {'git':
  ensure => latest,
}
```

