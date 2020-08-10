# Step 1: Review installation prerequisites on your Linux server

Before getting started, review this checklist to make sure you're ready to install PE.

**Note:** The examples in this guide use a Linux server running Red Hat Enterprise Linux \(RHEL\) 6.

1.  Be aware that you must work as the `root` user on the command line throughout the installation process.

2.  Make sure you meet the [hardware recommendations](https://puppet.com/docs/pe/2017.3/installing/hardware_requirements.html#hardware-requirements) for 10 or fewer nodes.

    You can download and install Puppet Enterprise on up to 10 nodes at no charge.

3.  Make sure that DNS is properly configured on the server you're installing on.

    -   All nodes must know their own hostnames, which you can achieve by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly. Setting the hostname usually involves the `hostname` command and one or more configuration files, but the exact method varies by platform.

    -   All nodes must be able to reach each other by name, which you can achieve with a local DNS server.

4.  Know the fully qualified domain name \(FQDN\) of the server you're installing PE on, for example, `master.example.com`.


**Related information**  


[Hardware requirements](hardware_requirements.md#)

