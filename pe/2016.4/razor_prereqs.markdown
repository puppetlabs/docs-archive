---
layout: default
title: "Setting up Razor environment"
canonical: "/pe/latest/razor_prereqs.html"

---

This workflow describes a sample setup using dnsmasq. It's not intended for production environments; however, you can use a similar workflow to set up Razor on any DHCP and TFTP service.

>**Warning**: We recommend first testing Razor in a completely isolated test environment. Running a second DHCP server on your company's network could bring down the network or replace a server with a fresh installation. See [Protecting existing nodes](./razor_brownfield.html) for strategies on avoiding data loss.

## Before you begin

[Install Puppet Enterprise](./install_basic.html).

## Install and configure dnsmasq DHCP/TFTP service

1. Use YUM to install dnsmasq:

        yum install dnsmasq

2. If it doesn't already exist, create the directory `/var/lib/tftpboot` .
3. Change the permissions for `/var/lib/tftpboot`:

        chmod 655 /var/lib/tftpboot

## Temporarily disable SELinux to enable PXE boot

1. Disable SELinux by changing the following setting in the file `/etc/sysconfig/selinux`:

        SELINUX=disabled

    >**Note**: Disabling SELinux is highly insecure and should only be done for testing  purposes.

    Another option is to craft an enforcement rule for SELinux that will enable PXE boot but will not completely disable SElinux.

2. Restart the computer and log in again.

## Edit dnsmasq.conf to enable DHCP

1. Edit `/etc/dnsmasq.conf`.  You must provide a DHCP range as indicated in this file.  For example, if you want an IP range from 10.0.1.50 - 10.0.1.120 with a 24 hour lease, your file should look like this:

       # Uncomment this to enable the integrated DHCP server, you need
       # to supply the range of addresses available for lease and optionally
       # a lease time. If you have more than one network, you will need to
       # repeat this for each network on which you want to supply DHCP
       # service.
       dhcp-range=10.0.1.50,10.0.1.120,24h

2. Indicating a range name can be helpful for more complex setups.  However, this is not required.

      dhcp-range=range1,10.0.1.50,10.0.1.120,24h
      dhcp-range=range2,10.0.1.121,10.0.1.222,48h

## Edit the dnsmasq configuration file to enable PXE boot

1. Edit the file `/etc/dnsmasq.conf`, by adding the following line at the bottom of the file:

        conf-dir=/etc/dnsmasq.d

2. Write and exit the file.
3. Create the file `/etc/dnsmasq.d/razor` and add the following configuration information:

        # This works for dnsmasq 2.45
        # iPXE sets option 175, mark it for network IPXEBOOT
        dhcp-match=IPXEBOOT,175
        dhcp-boot=net:IPXEBOOT,bootstrap.ipxe
        dhcp-boot=undionly-20140116.kpxe
        # TFTP setup
        enable-tftp
        tftp-root=/var/lib/tftpboot

4. Enable dnsmasq on boot:

        chkconfig dnsmasq on

5. Start the dnsmasq service:

        service dnsmasq start




* * *
