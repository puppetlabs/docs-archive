---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Install the job hardware OVA

Use the Continuous Delivery for PE job hardware open virtual appliance \(OVA\) to install and set up a preconfigured server used for testing Puppet code.

## About this task

## Procedure

1.  [Download the job hardware OVA.](https://d36ys68ofvtij3.cloudfront.net/puppet-pipelines-cdpe-jobhardware.ova)

2.  Import the OVA to your virtualization platform following the vendor's documentation.

    **Tip:** For most desktop virtualization platforms, double-clicking the OVA file launches the virtualization application and import the disk image.

3.  When the virtual machine \(VM\) is booted, it prints its IP address in the VM console, which you'll need in the next step.

4.  SSH into the virtual machine at its IP address.

    The default SSH credentials for the virtual machine are:

    -   Username: **pipelines**

    -   Password: **puppet**

5.  Run the setup script: `./setup-job-hardware`.

6.  When prompted, enter the IP address of the Continuous Delivery for PE OVA \(or your Continuous Delivery for PE instance\) and the username you selected when setting up your individual user account.

7.  Next, the setup script will install the Continuous Delivery agent. When prompted, enter your agent credential access token and secret key, which can be found in the Continuous Delivery for PE OVA's web UI by clicking **Settings** \> **Hardware Agents**.


## Result:

Once you see the `Success` message, navigate to to the Continuous Delivery for PE web UI and click **Hardware**. The job hardware OVA is shown on the list of servers.

