# Troubleshooting installation

If installation fails, check for these issues.

**Note:** If you encounter errors during installation, you can troubleshoot and run the installer as many times as needed.

## DNS is misconfigured

DNS must be configured correctly for successful installation.

1.  Verify that agents can reach the master hostname you chose during installation.

2.  Verify that the master can reach *itself* at the master hostname you chose during installation.

3.  If the master and console components are on different servers, verify that they can communicate with each other.


## Security settings are misconfigured

Firewall and security settings must be configured correctly for successful installation.

1.  On your master, verify that inbound traffic is allowed on ports 8140, 61613, and 443.

2.  If your master has multiple network interfaces, verify that the master allows traffic via the IP address that its valid DNS names resolve to, not just via an internal interface.


