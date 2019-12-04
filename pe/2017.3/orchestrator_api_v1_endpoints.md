# Puppet orchestrator API v1 endpoints

The Puppet orchestrator API v1 consists of the following endpoints.

|Endpoint|Use|
|--------|---|
|[`/command`](orchestrator_api_commands_endpoint.md#)|Start and stop orchestrator jobs.|
|[`/events`](orchestrator_api_events.endpoint.md#)|Provides details about events that occur when you run orchestrator jobs.|
|[`/inventory`](orchestrator_api_inventory.endpoint.md#)|Provides information about which nodes can be reached by the orchestrator.|
|[`/jobs`](orchestrator_api_jobs_endpoint.md#)|Allows you to gather information about any orchestrator jobs you run.|
|[`/tasks`](orchestrator_api_tasks_endpoint.md#)|Provides details about the tasks available on your system.|
|[`/orchestrator`](orchestrator_api_root.endpoint.md#)|Returns metadata about the orchestrator API.|

Refer to [forming requests](orchestrator_api_forming_requests.md) for instructions on interacting with this API.

-   **[Puppet orchestrator API: forming requests](orchestrator_api_forming_requests.md)**  
Requests to the orchestrator API should be well-formed HTTP\(S\) requests.
-   **[Puppet orchestrator API: commands endpoint](orchestrator_api_commands_endpoint.md#)**  
Use the `/command/deploy`, `/command/task`, and `/command/stop` endpoints to start and stop orchestrator jobs.
-   **[Puppet orchestrator API: events endpoint](orchestrator_api_events.endpoint.md#)**  
Use the `/events` endpoint to learn about events that occurred during an orchestrator job.
-   **[Puppet orchestrator API: inventory endpoint](orchestrator_api_inventory.endpoint.md#)**  
Use the `/inventory` endpoint to discover which nodes can be reached by the orchestrator.
-   **[Puppet orchestrator API: jobs endpoint](orchestrator_api_jobs_endpoint.md#)**  
Use the `/jobs` endpoint to examine orchestrator jobs and their details.
-   **[Puppet orchestrator API: tasks endpoint](orchestrator_api_tasks_endpoint.md#)**  
Use the `/tasks` endpoint to view details about the tasks pre-installed by PE and those you've installed.
-   **[Puppet orchestrator API: root endpoint](orchestrator_api_root.endpoint.md#)**  
Use the `/orchestrator` endpoint to returns metadata about the orchestrator API.
-   **[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)**  
From time to time, you may encounter an error using the orchestrator API. In such cases, you'll receive an error response.

