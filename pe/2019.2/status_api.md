# Status API

The status API allows you to check the health of PE components. It can be useful in automated monitoring of your PE infrastructure, removing unhealthy service instances from a load-balanced pool, checking configuration values, or when troubleshooting problems in PE.

You can check the overall health of pe-console-services, as well as the health of the individual services within pe-console-services:

-   Activity service
-   Node classifier
-   PuppetDB
-   Puppet Server
-   Role-based access control \(RBAC\)
-   Code Manager \(if configured\)

The endpoints provide overview health information in an overall healthy/error/unknown status field, and fine-detail information such as the availability of the database, the health of other required services, or connectivity to the Puppet master.

-   **[Authenticating to the status API](status_api_authenticating.md)**  
Token-based authentication is not required to access the status API. You can choose to authenticate requests by using whitelisted certificates, or can access the API without authentication via HTTP.
-   **[Forming requests to the status API](status_api_forming_requests.md)**  
The HTTPS status endpoints are available on the console services API server, which uses port 4433 by default.
-   **[JSON endpoints](status_api_json_endpoints.md#)**  
These two endpoints provide machine-consumable information about running services. They are intended for scripting and integration with other services.
-   **[Activity service plaintext endpoints](status_api_plaintext_endpoints.md#)**  
The activity service plaintext endpoints are designed for load balancers that don't support any kind of JSON parsing or parameter setting. They return simple string bodies \(either the state of the service in question or a simple error message\) and a status code relevant to the status result.
-   **[Metrics endpoints](status_api_metrics_endpoints.md#)**  
Puppet Server is capable of tracking advanced metrics to give you additional insight into its performance and health.
-   **[The metrics API](metrics_api.md#)**  
Puppet Enterprise includes an optional, enabled-by-default web endpoint for Java Management Extension \(JMX\) metrics, namely managed beans \(MBeans\).

**Related information**  


[API index](api_index.md#)

