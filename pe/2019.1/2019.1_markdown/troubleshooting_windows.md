# Troubleshooting Windows

Troubleshoot Windows issues with failed installations and upgrades, and failed or incorrectly applied manifests. Use the error message reference to solve your issues. Enable debugging.

## Installation fails

Check for these issues if Windows installation with Puppet fails.

### The installation package isn't accessible

The source of an MSI or EXE package must be a file on either a local filesystem, a network mapped drive, or a UNC path.

RI-based installation sources aren't supported, but you can achieve a similar result by defining a file whose source is the Puppet master and then defining a package whose source is the local file.

### Installation wasn't attempted with admin privileges

Puppet fails to install when trying to perform an unattended installation from the command line. A "norestart" message is returned, and installation logs indicate that installation is forbidden by system policy.

You must install as an administrator.

## Upgrade fails

The Puppet MSI package overwrites existing entries in the `puppet.conf` file. If you upgrade or reinstall using a different master hostname, Puppet applies the new value in `$confdir\puppet.conf`.

When you upgrade Windows, you must use the same master hostname that you specified when you installed.

For information on what settings are preserved during an upgrade, see installing [Puppet agent on Windows](installing_agents.md#).

## Errors when applying a manifest or doing a Puppet agent run

Check for the following issues if manifests cannot be applied or are not applied correctly on Windows nodes.

### Path or file separators are incorrect

For Windows, path separators must use a semi-colon \(;\), while file separators must use forward or backslashes as appropriate to the attribute.

In most resource attributes, the Puppet language accepts either forward or backslashes as the file separator. However, some attributes absolutely require forward slashes, and some attributes absolutely require backslashes.

When backslashes are double-quoted\("\), they must be escaped. When single-quoted \('\), they can be escaped. For example, these are valid file resources:

```
file { 'c:\path\to\file.txt': }
file { 'c:\\path\\to\\file.txt': }
file { "c:\\path\\to\\file.txt": }
```

But this is an invalid path, because `\p`, `\t`, and `\f` are interpreted as escape sequences:

```
file { "c:\path\to\file.txt": }
```

For more information, see the [language reference about backslashes on Windows.](https://puppet.com/docs/puppet/4.10/lang_windows_file_paths.html#using-backslashes-in-double-quoted-strings)

### Cases are inconsistent

Several resources are case-insensitive on Windows, like files, users, groups. However, these resources can be case sensitive in Puppet.

When establishing dependencies among resources, make sure to specify the case consistently. Otherwise, Puppet can't resolve dependencies correctly. For example, applying this manifest fails, because Puppet doesn't recognize that `ALEX` and `alex` are the same user:

```
file { 'c:\foo\bar':
  ensure => directory,
  owner  => 'ALEX'
}
user { 'alex':
  ensure => present
}
...
err: /Stage[main]//File[c:\foo\bar]: Could not evaluate: Could not find user ALEX
```

### Shell built-ins are not executed

Puppet doesn't support a shell provider on Windows, so executing shell built-ins directly fails.

Wrap the built-in in `cmd.exe`:

```
exec { 'cmd.exe /c echo foo':
  path => 'c:\windows\system32;c:\windows'
}
```

**Tip:** In the 32-bit versions of Puppet, you might encounter file system redirection, where system32 is switched to sysWoW64 automatically.

### PowerShell scripts are not executed

By default, PowerShell enforces a restricted execution policy which prevents the execution of scripts.

Specify the appropriate execution policy in the PowerShell command, for example:

```
exec { 'test':
  command => 'powershell.exe -executionpolicy remotesigned -file C:\test.ps1',
  path    => $::path
}
```

Or use the Puppet supported PowerShell.

### Services are referenced with display names instead of short names

Windows services support a short name and a display name, but Puppet uses only short names.

1.  Verify that your Puppet manifests use short names, for example `wuauserv`, not `Automatic Updates`.

## Error messages

Use this reference to troubleshoot error messages when using Windows with Puppet.

-   ```
Error: Could not connect via HTTPS to https://forge.puppet.com / Unable to verify the SSL certificate / The certificate may not be signed by a valid CA / The CA bundle included with OpenSSL may not be valid or up to date
```

    This error occurs when you run the `puppet module` subcommand on newly provisioned Windows nodes. The Forge uses an SSL certificate signed by the GeoTrust Global CA certificate. Newly provisioned Windows nodes might not have that CA in their root CA store yet.

    Download the "GeoTrust Global CA" certificate from GeoTrust's list of root certificates and manually install it by running `certutil -addstore Root GeoTrust_Global_CA.pem`.

-   ```
Service 'Puppet Agent' (puppet) failed to start. Verify that you have sufficient privileges to start system services.
```

    This error occurs when installing Puppet on a UAC system from a non-elevated account. Although the installer displays the UAC prompt to install Puppet, it does not elevate privileges when trying to start the service.

    Run from an elevated `cmd.exe` process when installing the MSI.

-   ```
Cannot run on Microsoft Windows without the sys-admin, win32-process, win32-dir, win32-service and win32-taskscheduler gems.
```

    This error occurs if you attempt to run Windows without required gems.

    Install specified gems: `gem install <GEM_NAME>`

-   "

    ```
    err: /Stage[main]//Scheduled_task[task_system]: Could not evaluate: The operation completed successfully.
    ```

    "

    This error occurs when using a the task scheduler gem earlier than version 0.2.1.

    Update the task scheduling gem: `gem update win32-taskscheduler`

-   ```
err: /Stage[main]//Exec[C:/tmp/foo.exe]/returns: change from notrun to 0 failed: CreateProcess() failed: Access is denied.
```

    This error occurs when requesting an executable from a remote Puppet master that cannot be executed.

    Set the user/group executable bits appropriately on the master:

    ```
    file { "C:/tmp/foo.exe":
      source => "puppet:///modules/foo/foo.exe",
    }
    
    exec { 'C:/tmp/foo.exe':
      logoutput => true
    }
    ```

-   ```
err: getaddrinfo: The storage control blocks were destroyed.
```

    This error occurs when the agent can't resolve a DNS name into an IP address or if the reverse DNS entry for the agent is wrong.

    Verify that you can run `nslookup <dns>`. If this fails, there is a problem with the DNS settings on the Windows agent, for example, the primary dns suffix is not set. For more information, see [Microsoft's documentation on Naming Hosts and Domains](http://technet.microsoft.com/en-us/library/cc959322.aspx).

-   ```
err: Could not request certificate: The certificate retrieved from the master does not match the agent's private key.
```

    This error occurs if the agent's SSL directory is deleted after it retrieves a certificate from the master, or when running the agent in two different security contexts.

    Elevate privileges using **Run as Administrator** when you select **Start Command Prompt with Puppet**.

-   ```
err: Could not send report: SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed. This is often because the time is out of sync on the server or client.
```

    This error occurs when Windows agents' time isn't synched. Windows agents that are part of an Active Directory domain automatically have their time synchronized with AD.

    For agents that are not part of an AD domain, enable and add the Windows time service manually:

    ```
    w32tm /register
    net start w32time
    w32tm /config /manualpeerlist:<ntpserver> /syncfromflags:manual /update
    w32tm /resync
    ```

-   ```
Error: Could not parse for environment production: Syntax error at '='; expected '}'
```

    This error occurs if `puppet apply -e` is used from the command line and the supplied command is surrounded with single quotes \('\), which causes `cmd.exe` to interpret any `=>` in the command as a redirect.

    Surround the command with double quotes \("\) instead.


## Logging and debugging

The Windows Event Log can be helpful when troubleshooting issues with Windows.

Enable Puppet to emit `--debug` and `--trace` messages to the Windows Event Log by stopping the Puppet service and restarting it:

```
c:\>sc stop puppet && sc start puppet --debug --trace
```

Note that this setting takes effect only until the next time the service is restarted, or until the system is rebooted.

For more information on logging, see [Configuring Puppet agent on Windows.](https://puppet.com/docs/puppet/5.3/services_agent_windows.html#logging-for-puppet-agent-on-windows-systems)

