---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Install the OVA to try Continuous Delivery for PE

Use the Continuous Delivery for Puppet Enterprise \(PE\) open virtual appliance \(OVA\) to install and set up the software without using Docker.

## Before you begin

You'll need a Continuous Delivery for PE license. See [Generate a license](generating_a_license.md) for instructions on creating a free 30-day trial license.

You'll also need a virtualization platform that supports the OVA disk format, such as:

-   Virtualbox

-   VMware Workstation for Windows

-   VMWare Fusion for Mac

-   VMware vSphere


## Procedure

1.  CAUTION:

Code Manager webhooks are not compatible with Continuous Delivery for PE. If your organization currently uses Code Manager webhooks to deploy code, you must dismantle these webhooks before installing Continuous Delivery for PE.

2.  Download the OVA from the [Continuous Delivery for PE downloads page](https://puppet.com/download-continuous-delivery-puppet-enterprise).

3.  Import the OVA to your virtualization platform following the vendor's documentation.

    **Tip:** For most desktop virtualization platforms, double-clicking the OVA file launches the virtualization application and import the disk image.

4.  When the virtual machine \(VM\) is booted, it prints its IP address in the VM console, which you'll need in the next step. Wait a few minutes for Continuous Delivery for PE to begin responding to requests.

    Logging into the VM isn't required, but you can do so with the following credentials:

    -   Username: **pipelines**

    -   Password: **puppet**

5.  Navigate to the Continuous Delivery for PE web UI at `http://<VM_IP_ADDRESS>:8080`. In a second browser tab. open the Artifactory web UI at `http://<VM_IP_ADDRESS>:8081`.

    **Tip:** If the VM is not responding at the IP address printed in its console, make sure the VM's network configuration allows traffic to reach the VM.

6.  Generate an Artifactory API key. Artifactory is used for storing Continuous Delivery for PE logs, job manifests, and artifacts.

    1.  In the Artifactory web UI, click **Log In** and enter the following credentials:

        -   Username: **admin**

        -   Password: **password**

    2.  Click **Welcome, admin** and select **Edit Profile**.

    3.  In the **Current Password** field, enter password and click **Unlock**.

    4.  Create an API key by clicking **Generate** ![](artifactory_generate.png).

    5.  Copy the generated key and save it somewhere safe. You'll use it when configuring Continuous Delivery for PE.

7.  In the Continuous Delivery for PE web UI, click **Get Started** and follow the prompts to configure your instance. You'll be asked to:

    1.  Select the email address and password to associate with the root account.

        **Note:** The root account is used for installing and configuring your organization's Continuous Delivery for PE instance.

    2.  Configure the endpoints for Continuous Delivery for PE services:

-   **WebUI**: `http://<VM_IP_ADDRESS>:8080`

-   **Backend Service**: `http://<VM_IP_ADDRESS>:8000`

-   **Agent Service**: `dump://<VM_IP_ADDRESS>:7000`

    3.  Configure storage. Select **Artifactory** and enter the following:
        -   **Artifactory Generic Binary Repository Name**: generic-local

        -   **Artifactory Endpoint**: `http://<VM_IP_ADDRESS>:8081`

        -   Paste the Artifactory API key you generated earlier into the **Artifactory Access Token** field.

        -   Leave the **Path Prefix** field blank.

    4.  Upload a Continuous Delivery for PE license file. See [Generate a license](generating_a_license.md) for instructions on creating a free 30-day trial license.
    5.  Finally, secure the virtual machine by updating the SSH password. The default SSH credentials for the virtual machine are:

        -   Username: **pipelines**

        -   Password: **puppet**

8.  When configuration is complete, you'll see a success message. Click **Create your user account** to sign out of the root account, then create your individual user account.

    **Tip:** If your organization uses GitHub, return to the root console and set up your GitHub OAuth application before signing out as root. See [Integrate with GitHub](integrations.md#) for instructions.

9.  If you wish to run job hardware \(used for testing Puppet code\) on the same VM, complete the following steps:

    **Note:** You can also [configure a server as job hardware](configure_job_hardware.md#), or use a second VM to run the [job hardware OVA](install_job_hardware_ova.md).

    1.  Run the setup script: `./setup-job-hardware`.

    2.  When prompted, enter the IP address of the Continuous Delivery for PE OVA and the username you selected when setting up your individual user account.

    3.  Next, the setup script will install the Continuous Delivery agent. When prompted, enter your agent credential access token and secret key, which can be found in the Continuous Delivery for PE OVA's web UI by clicking **Settings** \> **Hardware Agents**.

    4.  Once you see the `Success` message, navigate to to the Continuous Delivery for PE web UI and click **Hardware**. Your new job hardware is shown on the list of servers.


## Result:

Now that the Continuous Delivery for PE OVA is installed, move on to these next steps:

-   [Integrate your source control system.](integrations.md#)

-   [Integrate with Puppet Enterprise.](integrate_with_puppet_enterprise.md#)

-   If you didn't do so in step 8, configure job hardware, which is used when testing your Puppet code. You can configure a [standalone server](configure_job_hardware.md#), or install the [job hardware OVA](install_job_hardware_ova.md).

-   [Configure impact analysis.](configure_impact_analysis.md#)

-   Follow our [Getting started with Continuous Delivery for PE](getting_started.md#) guide to learn about core workflows and capabilities.




