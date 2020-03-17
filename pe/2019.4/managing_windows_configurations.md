# Managing Windows configurations

This page covers the different ways you can use Puppet Enterprise \(PE\) to manage your Windows configurations, including creating local group and user accounts.

-   **[Basic tasks and concepts in Windows](basic_tasks_and_concepts_in_windows.md)**  
This section is meant to help familiarize you with several common tasks used in Puppet Enterprise \(PE\) with Windows agents, and explain the concepts and reasons behind performing them.
-   **[Manage Windows services](managing_windows_services.md)**  
You can use Puppet to manage Windows services, specifically, to start, stop, enable, disable, list, query, and configure services. This way, you can ensure that certain services are always running or are disabled as necessary.
-   **[Manage Windows users and groups](manage_windows_users_and_groups.md)**  
 Puppet can be used to create local group and user accounts. Local user accounts are often desirable for isolating applications requiring unique permissions.
-   **[Executing PowerShell code](executing_powershell_code.md)**  
Some Windows maintenance tasks require the use of Windows Management Instrumentation \(WMI\), and PowerShell is the most useful way to access WMI methods. Puppet has a special module that can be used to execute arbitrary PowerShell code.
-   **[Using templates to better manage Puppet code](using_templates_to_manage_puppet_code.md)**  
While inline PowerShell is usable as an `exec` resource in your manifest, such code can be difficult to read and maintain, especially when it comes to handling escaping rules.

**Parent topic:**[Manage your Windows infrastructure](manage_windows_infrastructure.md)

