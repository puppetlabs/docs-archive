---
author: melissa amos <melissa.amos@puppet.com\>
---

# Provisioning with Razor

Razor is a provisioning application that deploys bare-metal systems.

Policy-based provisioning lets you use characteristics of the hardware as well as user-provided data to make provisioning decisions. You can automatically discover bare-metal hardware, dynamically configure operating systems and hypervisors, and hand off nodes to Razor for workload configuration.

Automated provisioning makes Razor ideal for big installation jobs, like setting up a new selection of servers in a server farm. You can also use Razor to regularly wipe and re-provision test machines.

-   **[How Razor works](how_razor_works.md#)**  
There are five key steps for provisioning nodes with Razor.
-   **[Setting up a Razor environment](setting_up_a_razor_environment.md#)**  
Razor relies on a PXE environment to boot the Razor microkernel. You must set up your PXE environment before you can successfully provision with Razor.
-   **[Installing Razor](installing_razor.md#)**  
After you set up a Razor environment, you're ready to install Razor.
-   **[Using the Razor client](using_the_razor_client.md#)**  
There are three ways to communicate with the Razor server.
-   **[Protecting existing nodes](protecting_existing_nodes.md#)**  
In brownfield environments – those in which you already have machines installed that PXE boot against the Razor server – you must take extra precautions to protect existing nodes. Failure to adequately protect existing nodes can result in data loss.
-   **[Provisioning a \*nix node](provisioning_a_nix_node.md#)**  
Provisioning deploys and installs your chosen operating system to target nodes.
-   **[Provisioning a Windows node](provisioning_a_windows_node.md#)**  
Provisioning deploys and installs your chosen operating system to target nodes.
-   **[Provisioning with custom facts](provisioning_with_custom_facts.md#)**  
You can use a microkernel extension to provision nodes based on hardware info or metadata that isn’t available by default in Facter.
-   **[Working with Razor objects](working_with_razor_objects.md)**  
Provisioning with Razor requires certain objects that define how nodes are provisioned.
-   **[Using the Razor API](using_the_razor_api.md#)**  
The Razor API is REST-based. The default URL for the API entrypoint is `https://razor:8151/api`.
-   **[Upgrading Razor](upgrading_razor.md#)**  
If you used Razor in a previous Puppet Enterprise environment, upgrade Razor to keep your Puppet Enterprise and Razor versions synched.
-   **[Uninstalling Razor](uninstalling_razor.md#)**  
If you're permanently done provisioning nodes, you can uninstall Razor.

