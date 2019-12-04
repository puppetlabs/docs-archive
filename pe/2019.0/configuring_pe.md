# Configuring Puppet Enterprise

**Important:** PE shares configuration settings used in open source Puppet and documented in the [Configuration Reference](https://puppet.com/docs/puppet/latest/configuration.html), however PE defaults for certain settings might differ from the Puppet defaults. Some examples of settings that have different PE defaults include `disable18n`, `environment_timeout`,`always_retry_plugins`, and the Puppet Server JRuby `max-active-instances` setting. To verify PE configuration defaults, check the `puppet.conf` file after installation.

-   **[Methods for configuring Puppet Enterprise](config_intro.md#)**  
After you've installed Puppet Enterprise \(PE\), optimize it for your environment by configuring and tuning settings as needed. For example, you might want to add your own certificate to the whitelist, increase the max-threads setting for `http` and `https` requests, or configure the number of JRuby instances.
-   **[Configuring and tuning Puppet Server](config_puppetserver.md#)**  
After you've installed Puppet Enterprise, optimize it for your environment by configuring and tuning Puppet Server settings as needed.
-   **[Configuring and tuning the console](config_console.md#)**  
After installing Puppet Enterprise, you can change product settings to customize the console's behavior, adjust to your team's needs, and improve performance.
-   **[Configuring and tuning PuppetDB](config_puppetdb.md#)**  
After you've installed Puppet Enterprise, optimize it for your environment by configuring and tuning PuppetDB configuration as needed.
-   **[Configuring and tuning orchestration](config_orchestration.md#)**  
After installing PE, you can change some default settings to further configure the orchestrator and pe-orchestration-services.
-   **[Configuring proxies](configuring_proxies.md#)**  
You can work around limited internet access by configuring proxies at various points in your infrastructure, depending on your connectivity limitations.
-   **[Configuring Java arguments](config_java_args.md#)**  
You might need to increase the Java Virtual Machine \(JVM\) memory allocated to Java services to improve performance in your Puppet Enterprise \(PE\) deployment.
-   **[Configuring ulimit for PE services](config_ulimit.md#)**  
As your infrastructure grows and you bring more agents under management, you may need to increase the number allowed file handles per client.
-   **[Tuning monolithic installations](tuning_monolithic.md#)**  
Use these guidelines to configure your installation to maximize its use of available system \(CPU and RAM\) resources.
-   **[Writing configuration files](config_files.md#)**  
 Puppet supports two formats for configuration files that configure settings: valid JSON and Human-Optimized Config Object Notation \(HOCON\), a JSON superset.
-   **[Analytics data collection](analytics_overview.md#)**  
Some components automatically collect data about how you use Puppet Enterprise. If you want to opt out of providing this data, you can do so, either during or after installing.
-   **[Static catalogs in Puppet Enterprise](static_catalogs.md#)**  
A static catalog is a specific type of Puppet catalog that includes metadata that specifies the desired state of any file resources on a node that have `source` attributes. Using static catalogs can reduce the number of requests an agent makes to the master.

