---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configuring and adding integrations

Configure your Continuous Delivery for Puppet Enterprise \(PE\) instance so that it communicates with your source control system, Puppet Enterprise, and the job hardware you use to run tests on your Puppet code.

-   **[Integrate with source control](integrations.md#)**  
Integrate your source control system with Continuous Delivery for Puppet Enterprise \(PE\) by following the appropriate set of instructions on this page.
-   **[Integrate with Puppet Enterprise](integrate_with_puppet_enterprise.md#)**  
To set up an integration between your Puppet Enterprise \(PE\) instance and Continuous Delivery for PE, you must first set up a dedicated PE user with appropriate permissions, then add your PE instance's credentials to Continuous Delivery for PE.
-   **[Configure impact analysis](configure_impact_analysis.md#)**  
In order to use impact analysis, you must configure both your Puppet Enterprise \(PE\) and Continuous Delivery for PE instances. This process involves generating a new certificate, installing a dedicated module, updating classification of your Puppet master, and adding credentials to Continuous Delivery for PE.
-   **[Configure job hardware](configure_job_hardware.md#)**  
Job hardware, or the servers Continuous Delivery for Puppet Enterprise \(PE\) uses to run tests on your Puppet code, must be configured before you can begin running tests or using pipelines. 
-   **[Analytics data collection](cd_analytics.md#)**  
Continuous Delivery for Puppet Enterprise \(PE\) automatically collects data about how you use the software. If you want to opt out of providing this data, you can do so when installing Continuous Delivery for PE.
-   **[Configure LDAP](configure_ldap.md#)**  
Continuous Delivery for Puppet Enterprise \(PE\) supports use of the Lightweight Directory Access Protocol \(LDAP\) for managing user authentication. Once an LDAP configuration is in place, use group mapping to associate your existing LDAP groups with role-based access control \(RBAC\) groups in Continuous Delivery for PE.
-   **[Configure SMTP](configure_smtp.md)**  
Configure SMTP for your Continuous Delivery for Puppet Enterprise \(PE\) installation so that users can receive email notifications from the software.
-   **[Configure SSL](configure_ssl.md#)**  
 Continuous Delivery for Puppet Enterprise \(PE\) supports the use of Secure Sockets Layer \(SSL\) for enhanced security when using the software.

