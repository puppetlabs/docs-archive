# Common configuration tasks

This selection of common configuration tasks gives you just a sample of the things you can manage with Puppet Enterprise. These steps provide an excellent introduction to the capabilities of PE.

PE enables you to manage processes like time syncing, elevated privileges, and firewall rules from one central location.

-   **[Managing NTP with PE](managing_ntp_with_pe.md#)**  
The clocks on your servers need to synchronize with something to let them know what the right time is. NTP is a protocol that synchronizes computer clocks over a network to within a millisecond, using Coordinated Universal Time \(UTC\). Follow this guide to get time synced across all your PE-managed nodes.
-   **[Managing a DNS nameserver with PE](managing_dns_nameserver_pe.md#)**  
A nameserver ensures that the human-readable names you type in your browser \(for example, `google.com`\) can be resolved to IP addresses that computers can read. This guide provides instructions for getting started managing a simple DNS nameserver file with PE.
-   **[Managing SSH with Puppet](ssh_getting_started_guide.md#)**  
This guide provides instructions for getting started managing SSH across your PE deployment using a module from the Puppet Forge.
-   **[Managing sudo with PE](sudo_users_getting_started.md#)**  
Managing sudo on your agent nodes allows you to control which system users have access to elevated privileges. This guide provides instructions for getting started managing sudo privileges across your nodes, using a module from the Puppet Forge in conjunction with a simple module you write.
-   **[Managing firewalls with PE](firewall_getting_started.md#)**  
Follow the steps in this guide to get started managing firewall rules with the puppet-firewall module and a simple module you’ll write that defines those rules.
-   **[Managing Windows configurations](managing_windows_configurations.md#)**  
This page covers the different ways you can use Puppet Enterprise \(PE\) to manage your Windows configurations, including creating local group and user accounts.
-   **[Installing and using Windows modules](installing_and_using_windows_modules.md#)**  
This guide covers creating a managed permission with `ACL`, creating managed registry keys and values with `registry`, and installing and creating your own packages with `chocolatey`.

