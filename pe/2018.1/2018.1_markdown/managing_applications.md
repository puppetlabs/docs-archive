---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Managing applications

Application orchestration provides Puppet language extensions and command-line tools to help you configure and manage multi-service and multi-node applications.

CAUTION:

Application orchestration is deprecated and will be removed in a future release.

-   **[Application orchestration](application_orchestration_overview.md#)**  
Create and manage multi-service and multi-node applications.
-   **[Deploying applications with Puppet Application Orchestration: workflow](deploying_applications_workflow.md#)**  
Although Puppet Application Orchestration can help you manage any distributed set of infrastructure, it's primarily designed to configure an application stack. The simple application stack used in the following extended example comprises a database server on one machine and a web server that connects to the database on another machine.
-   **[Creating application definitions](creating_application_definitions.md#)**  
When you create an applications using the Puppet language, you model it in an application definition. An application definition consists of two parts: application components and service resources.
-   **[Declaring application instances](declaring_application_instances.md#)**  
Declaring an application instance means you have instantiated the application you modeled and have assigned the application's components to nodes and added resource mapping statements.
-   **[Producing and consuming service resources](producing_and_consuming_service_resources.md#)**  
When you compose applications, application components can share information with each other by exporting and consuming environment-wide **service resources**.
-   **[Writing custom service resource types](writing_custom_service_resource_types.md)**  
When writing custom service resource types, remember that service resources typically adhere to the same format as standard custom types.
-   **[Availability tests](availability_tests.md#)**  
Availability tests are custom resource providers for testing service resources when you configure applications. They can be used to ensure your nodes are correctly configured and services are running before Puppet configures dependent services on another node.

