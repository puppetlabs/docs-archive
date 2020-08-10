---
author: Suzie Baunsgard <suzanne@puppet.com\>
---

# Troubleshooting

Use this guide to troubleshoot issues with your Puppet Enterprise installation.

-   **[Troubleshooting installation](troubleshooting_the_pe_installer.md#)**  
If installation fails, check for these issues.
-   **[Troubleshooting high availability](troubleshooting_ha.md)**  
If high availability commands fail, check for these issues.
-   **[Troubleshooting puppet infrastructure run commands](troubleshooting_puppet_infra.md)**  
If `puppet infrastructure run` commands fail, review the logs at `/var/log/puppetlabs/installer/bolt_info.log` and check for these issues.
-   **[Troubleshooting connections between components](troubleshooting_communications_between_components.md#)**  
If agent nodes can't retrieve configurations, check for communication, certificate, DNS , and NTP issues.
-   **[Troubleshooting the databases](troubleshooting_the_databases.md#)**  
If you have issues with the databases that support the console, make sure that the PostgreSQL database is not too large or using too much memory, that you don't have port conflicts, and that `puppet apply` is configured correctly.
-   **[Troubleshooting backup and restore](troubleshooting_backup_restore.md)**  
If backup or restore fails, check for these issues.
-   **[Troubleshooting Code Manager](code_mgr_troubleshoot.html)**  

-   **[Troubleshooting Windows](troubleshooting_windows.md#)**  
Troubleshoot Windows issues with failed installations and upgrades, and failed or incorrectly applied manifests. Use the error message reference to solve your issues. Enable debugging.

