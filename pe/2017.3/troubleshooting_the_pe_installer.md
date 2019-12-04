# Troubleshooting the installer

If the installer fails, check for configuration or installation issues.

**Note:** If you encounter errors during installation, you can troubleshoot and run the installer as many times as needed.

## DNS is misconfigured

DNS must be configured correctly for successful installation.

### About this task

### Procedure

1.  Verify that Puppet agents can reach the Puppet master hostname you chose during installation.

2.  Verify that the Puppet master can reach *itself* at the Puppet master hostname you chose during installation.

3.  If the master and console components are on different servers, verify that they can communicate with each other.


## Security settings are misconfigured

Firewall and security settings must be configured correctly for successful installation.

### About this task

### Procedure

1.  Verify that inbound traffic is allowed on required ports.

    If you installed the Puppet master and the console on the same server, it must accept inbound traffic on ports 8140, 61613, and 443.

    If you installed the master and the console on different servers, the master must accept inbound traffic on ports 8140 and 61613 and the console must accept inbound traffic on ports 8140 and 443.

2.  If your Puppet master has multiple network interfaces, verify that the master allows traffic via the IP address that its valid DNS names resolve to, not just via an internal interface.


## The console was installed before the Puppet master

If you are installing the console and the Puppet master on separate servers, you must install the console first.

