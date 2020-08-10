---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Setting up a Razor environment

Razor relies on a PXE environment to boot the Razor microkernel. You must set up your PXE environment before you can successfully provision with Razor.

There are two ways to enable PXE boot:

-   \(Recommended\) Integrate Razor with the DHCP solution of the system being provisioned.

-   Rely on UEFI to directly load the .ipxe file that’s required to boot the Razor microkernel.


Because it’s difficult to guarantee that all of your hardware is UEFI-enabled, configuring DHCP is the preferred method for setting up a Razor environment.

## Set up a Razor environment

Set up a PXE environment using the DHCP and TFTP service of your choice.

You must have Puppet Enterprise installed.

This workflow describes a sample setup using dnsmasq. Dnsmasq is not intended for production environments; however, you can use a similar workflow to set up DHCP and TFTP with more robust solutions.

**Important:** Set up and test PXE in a completely isolated test environment. Running a second DHCP server on your company’s network could bring down the network or replace a server with a fresh installation. See [Protecting existing nodes](protecting_existing_nodes.md#) for strategies on avoiding data loss.

### Install and configure dnsmasq DHCP-TFTP service

Install dnsmasq to manage communication between nodes and the Razor server. When a node boots, dnsmasq forwards the booted node to the Razor service.

1.  Use YUM to install dnsmasq: `yum install dnsmasq`

2.  If it doesn't already exist, create the directory `/var/lib/tftpboot`.

3.  Change the permissions for the TFTP boot directory: `chmod 655 /var/lib/tftpboot`.


### Temporarily disable SELinux

You must temporarily disable SELinux in order to enable PXE boot. Alternatively, you could craft an enforcement rule for SELinux that enables PXE boot but doesn't completely disable SELinux.

1.  In the file `/etc/sysconfig/selinux`, set `SELINUX=disabled`.

2.  Restart the computer.


### Edit dnsmasq.conf to enable DHCP

You must specify a DHCP range to enable communication with your DHCP server.

**Tip:** For more complex setups, it might be helpful to indicate a range name, for example: `dhcp-range=range1,10.0.1.50,10.0.1.120,24h dhcp-range=range2,10.0.1.121,10.0.1.222,48h`.

1.  Edit `/etc/dnsmasq.conf`to specify a DHCP range.

    For example, for an IP range from 10.0.1.50 - 10.0.1.120 with a 24-hour lease, your file resembles: 

    ```
    # Uncomment this to enable the integrated DHCP server, you need
    # to supply the range of addresses available for lease and optionally
    # a lease time. If you have more than one network, you must
    # repeat this for each network on which you want to supply DHCP
    # service.
    dhcp-range=10.0.1.50,10.0.1.120,24h
    ```


### Edit the dnsmasq configuration file to enable PXE boot

Use dnsmasq to enable PXE booting on nodes.

1.  At the bottom of the `/etc/dnsmasq.conf` file, add: `conf-dir=/etc/dnsmasq.d`

2.  Write and exit the file.

3.  Create the file `/etc/dnsmasq.d/razor` and add configuration information. 

    For example, for dnsmasq 2.45:

    ```
     # iPXE sets option 175, mark it for network IPXEBOOT
     dhcp-match=IPXEBOOT,175
     dhcp-boot=net:IPXEBOOT,bootstrap.ipxe
     dhcp-boot=undionly-20140116.kpxe
     # TFTP setup
     enable-tftp
     tftp-root=/var/lib/tftpboot
    ```

4.  Enable dnsmasq on boot: `chkconfig dnsmasq on`

5.  Start the dnsmasq service: `service dnsmasq start`


