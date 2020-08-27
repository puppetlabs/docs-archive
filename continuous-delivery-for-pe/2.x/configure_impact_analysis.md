---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configure impact analysis

In order to use impact analysis, you must configure both your Puppet Enterprise \(PE\) and Continuous Delivery for PE instances. This process involves generating a new certificate, installing a dedicated module, updating classification of your Puppet master, and adding credentials to Continuous Delivery for PE.

For best results, work through the configuration process in the order presented here.

## What is impact analysis?

Impact analysis is a Continuous Delivery for PE tool that lets you see the potential impact that new Puppet code will have on your PE-managed infrastructure, even before the new code is merged. When you add impact analysis to a control repo's pipeline, Continuous Delivery for PE automatically generates a report on every proposed code change to that control repo.

See [Previewing the impact of code changes](impact_analysis.md#) for more on impact analysis.

## Before you begin

Make sure you're ready to get started by completing the following:

-   [Install Continuous Delivery for PE](install_set_up.md).

**Important:** If you are running PE version 2019.1.x or 2019.2.x and have integrated your PE instance with Continuous Delivery for PE, **you do not need to complete this configuration**. Impact analysis was automatically configured for you during the integration.

-   [Integrate with your source control system](integrations.md#).

-   [Integrate with Puppet Enterprise](integrate_with_puppet_enterprise.md#).


## Generate a dedicated Puppet Enterprise certificate

A dedicated PE certificate is required to authenticate with the endpoints used for impact analysis. For security purposes, make sure you generate a new certificate.

### About this task

**Note:** This certificate is not counted as one of your PE node licenses.

### Procedure

1.  On the node hosting your PE certificate authority service \(typically the Puppet master\), run the command appropriate to your version of PE. 

    -   For PE version 2019.1 or 2019.2: `puppetserver ca generate --certname <CERTIFICATE_NAME>`

    -   For PE version 2018.1: `puppet cert generate <CERTIFICATE_NAME> `

    Replace `<CERTIFICATE_NAME>` with the name you wish to give the certificate, such as cd4pe-impact-analysis.


## Install modules

Impact analysis requires you to install the `puppetlabs-cd4pe` module and its dependent modules.

### About this task

The `puppetlabs-cd4pe` module must be used with seven dependent modules. The eight modules and their required versions are as follows:

|Module|Required version|
|------|----------------|
|`[puppetlabs-cd4pe](https://forge.puppet.com/puppetlabs/cd4pe)`|1.1.0 or later|
|`[puppetlabs-stdlib](https://forge.puppet.com/puppetlabs/stdlib/4.19.0/readme)`|4.19.0 or later|
|`[puppetlabs-puppet\_authorization](https://forge.puppet.com/puppetlabs/puppet_authorization)`|0.5.0|
|`[puppetlabs-hocon](https://forge.puppet.com/puppetlabs/hocon/0.9.3/readme)`|0.9.3 or later in the 0.x or 1.x series|
|`[puppetlabs-concat](https://forge.puppet.com/puppetlabs/concat/1.1.1/readme)`|1.1.1 or later in the 1.x, 2.x, 3.x, or 4.x series|
|`[puppetlabs-docker](https://forge.puppet.com/puppetlabs/docker)`|3.3.0 or later|
|`[puppetlabs-apt](https://forge.puppet.com/puppetlabs/apt)`|4.4.1 or later|
|`[puppetlabs-translate](https://forge.puppet.com/puppetlabs/translate)`|1.1.0 or later|

### Procedure

1.  Add the eight modules listed above to the Puppetfile for each environment against which your compilers compile catalogs.

    A sample Puppetfile entry:

    ```
    mod 'puppetlabs-cd4pe', :latest
    # Requirements for cd4pe
    mod 'puppetlabs-concat', '4.2.1'
    mod 'puppetlabs-hocon', '1.0.1'
    mod 'puppetlabs-puppet_authorization', '0.5.0'
    mod 'puppetlabs-stdlib', '4.25.1'
    mod 'puppetlabs-docker', '3.3.0'
    mod 'puppetlabs-apt', '6.2.1'
    mod 'puppetlabs-translate', '1.1.0'
    ```

2.  Deploy the updated code to the relevant environments by running `puppet code deploy <ENVIRONMENT>`.

    **Remember:** Continuous Delivery for PE performs this step for you if you've configured it to deploy code changes automatically to the relevant environments.


## Update classification

Once `puppetlabs-cdpe` and its dependencies have been deployed, you must update classification of your nodes and whitelist the certificate you generated for impact analysis.

### About this task

### Procedure

1.  In the PE console, click **Classification** and open the **PE Infrastructure** group.

2.  Select the **PE Master** group and click **Configuration**.

3.  In the **Add new class** field, select **cd4pe::impact\_analysis** and click **Add class**, then commit your change.

    If you don't find **cd4pe::impact\_analysis** in the class list, click **Refresh** to update class definitions.

4.  In the newly added **cd4pe::impact\_analysis** class, select the **whitelisted\_certnames** parameter.

5.  In the **Value** field, enter an array containing the name of the certificate you generated earlier, using the following format: `["<CERTIFICATE_NAME>"]`.

    CAUTION:

    Do not include angle brackets \(< and \>\) in the array.

6.  Run Puppet on the nodes in the **PE Master** group.  

    **Important:** This Puppet run restarts the pe-puppetserver service.


## Add credentials to Continuous Delivery for PE

The final step in the impact analysis configuration process is to update your PE instance's credentials in the Continuous Delivery for PE web UI.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  Click **Puppet Enterprise**. Click **Edit** ![](edit.png) for the PE instance you're adding impact analysis to.  

3.  Click **Impact Analysis Credentials** to display the configuration controls.

4.  Enter the endpoint for the Puppet Server service. You can locate this endpoint with the PE console.

    1.  In the PE console, click **Overview**, then click **Puppet Services status**.

    2.  Copy the Puppet Server endpoint from the Puppet Services status monitor.

    3.  In the Continuous Delivery for PE web UI, paste the endpoint into the **Puppet Server Service** field.

5.  Retrieve the new PE certificate you generated earlier and its private key from your Puppet Server certificate authority service by running the following:  

    ```
    cat /etc/puppetlabs/puppet/ssl/certs/<CERTIFICATE_NAME>.pem
    cat /etc/puppetlabs/puppet/ssl/private_keys/<CERTIFICATE_NAME>.pem
    ```

6.  Copy the certificate in its entirety \(including the header and footer\) and paste it in the **Impact Analysis Certificate** field. Then copy the private key in its entirety \(including the header and footer\) and paste it in the **Impact Analysis Private Key** field.

7.  Click **Save Changes**.


### Result:

Continuous Delivery for PE validates that everything is configured correctly. Once you receive a success message, you're ready to use impact analysis.

To get started, see [Previewing the impact of code changes](impact_analysis.md#).

