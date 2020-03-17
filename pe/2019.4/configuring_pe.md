# Configuring Puppet Enterprise

-   **[Methods for configuring Puppet Enterprise](config_intro.md#)**  
After you've installed Puppet Enterprise \(PE\), optimize it for your environment by configuring and tuning settings as needed. For example, you might want to add your own certificate to the whitelist, increase the max-threads setting for `http` and `https` requests, or configure the number of JRuby instances.
-   **[Configuring and tuning Puppet Server](config_puppetserver.md#)**  
After you've installed Puppet Enterprise, optimize it for your environment by configuring and tuning Puppet Server settings as needed.
-   **[Configuring and tuning the console](config_console.md#)**  
After installing Puppet Enterprise, you can change product settings to customize the console's behavior, adjust to your team's needs, and improve performance. Many settings can be configured in the console itself.
-   **[Configuring and tuning security settings](configuring_security_settings.md)**  
Ensure your PE environment is secure by configuring security settings.
-   **[Configuring and tuning PuppetDB](config_puppetdb.md#)**  
After you've installed Puppet Enterprise, optimize it for your environment by configuring and tuning PuppetDB configuration as needed.
-   **[Configuring and tuning orchestration](config_orchestration.md#)**  
After installing PE, you can change some default settings to further configure the orchestrator and pe-orchestration-services.
-   **[Configuring proxies](configuring_proxies.md#)**  
You can work around limited internet access by configuring proxies at various points in your infrastructure, depending on your connectivity limitations.
-   **[Configuring Java arguments](config_java_args.md#)**  
You might need to increase the Java Virtual Machine \(JVM\) memory allocated to Java services to improve performance in your Puppet Enterprise \(PE\) deployment.
-   **[Configuring ulimit for PE services](config_ulimit.md#)**  
As your infrastructure grows and you bring more agents under management, you might need to increase the number allowed file handles per client.
-   **[Tuning standard installations](tuning_monolithic.md#)**  
Use these guidelines to configure your installation to maximize its use of available system \(CPU and RAM\) resources.
-   **[Writing configuration files](config_files.md#)**  
 Puppet supports two formats for configuration files that configure settings: valid JSON and Human-Optimized Config Object Notation \(HOCON\), a JSON superset.
-   **[Analytics data collection](analytics_overview.md#)**  
Some components automatically collect data about how you use Puppet Enterprise. If you want to opt out of providing this data, you can do so, either during or after installing.
-   **[Static catalogs in Puppet Enterprise](static_catalogs.md#)**  
A catalog is a document that describes the desired state for each resource that Puppet manages on a node. A master typically compiles a catalog from manifests of Puppet code. A static catalog is a specific type of Puppet catalog that includes metadata that specifies the desired state of any file resources containing `source` attributes pointing to `puppet:///` locations on a node.

