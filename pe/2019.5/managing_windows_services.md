# Manage Windows services

You can use Puppet to manage Windows services, specifically, to start, stop, enable, disable, list, query, and configure services. This way, you can ensure that certain services are always running or are disabled as necessary.

## About this task

You write Puppet code to manage services in the manifest. When you apply the manifest, the changes you make to the service are applied.

**Note:** In addition to using manifests to apply configuration changes, you can query system state using the `puppet resource` command, which emits code as well as applying changes.

-   **[Ensure a Windows service is running](ensure_a_windows_service_is_running.md)**  
There are often services that you always want running in your infrastructure.
-   **[Stop a Windows service](stop_a_windows_service.md)**  
Some services can impair performance, or might need to be stopped for regular maintenance.
-   **[Schedule a recurring task](schedule_recurring_windows_task.md)**  
Regularly scheduled tasks are often necessary on Windows to perform routine system maintenance.

