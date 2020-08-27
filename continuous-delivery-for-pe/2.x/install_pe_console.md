---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Install Continuous Delivery for PE from the PE console

Users of supported PE versions who are running Enterprise Linux 7 on their Puppet master can install Continuous Delivery for PE directly from the PE console. This installation method is available for PE 2019.2.x, 2019.1.x, and PE 2018.1.8 and later versions in the 2018.1.x series.

## Before you begin

Review the [system requirements](cd_system_requirements.md#) and ensure you have:

-   A designated node where you'll install Continuous Delivery for PE. This node must be running Enterprise Linux 7, and must have Puppet installed.

**Important:** To install Continuous Delivery for PE from the PE console, you must be running Enterprise Linux 7 in one of the following distributions on your Puppet master.

-   CentOS
-   Oracle Linux
-   Red Hat Enterprise Linux
-   Scientific Linux
For all other operating systems and versions, see [Install Continuous Delivery for PE using the cd4pe module](install_module.md#).

-   The PE user permission to run tasks on this node.


CAUTION:

Code Manager webhooks are not compatible with Continuous Delivery for PE. If your organization currently uses Code Manager webhooks to deploy code, you must dismantle these webhooks before installing Continuous Delivery for PE.

## Install Continuous Delivery for PE from the PE 2019.2.x or 2019.1.x console

Users of the PE 2019.1.x or 2019.2.x series can install Continuous Delivery for PE directly from the PE console with just a few clicks.

### About this task

### Procedure

1.  In the PE console, click **Integrations**.

2.  In the **Continuous Delivery for PE host** field, enter the name of the node where you wish to install Continuous Delivery for PE.

    **Note:** This node must be running Enterprise Linux 7, and must have Puppet installed.

3.  In the **Administrator email** field, enter the email address for the Continuous Delivery for PE administrator, also known as the root user. This email address is used as your username when signing in to Continuous Delivery for PE as the root user.

4.  In the **Administrator password** field, enter a strong password for the Continuous Delivery for PE administrator account.

5.  To perform a default installation of Continuous Delivery for PE, click **Install**. If you need to customize your installation, click **Advanced options** and see [Advanced configuration options](install_module.md#).

    **Important:** In order to use the default installation, your Puppet certificate name must be a resolvable DNS hostname. If that is not the case, you must set the `resolvable_hostname` parameter to a resolvable address where the Continuous Delivery for PE server is reachable. You can use the `${trusted[certname]}` fact to set this parameter.

6.  When the job is complete, navigate to the URL printed on the task page. Click **Trial Mode** to start a free seven-day trial. Once this period is complete, you'll be prompted to generate and upload a license. See [Generate a license](generating_a_license.md) for instructions on creating a free 30-day trial license.


### Result:

Now that Continuous Delivery for PE is installed, [create your individual user account](create_a_user_account.md) and then move on to these next steps:

-   [Integrate your source control system.](integrations.md#)

-   [Integrate with Puppet Enterprise](integrate_with_puppet_enterprise.md#), which automatically configures impact analysis in PE 2019.1.x and 2019.2.x.

-   [Configure job hardware](configure_job_hardware.md#), which is used when testing your Puppet code.

-   Follow our [Getting started with Continuous Delivery for PE](getting_started.md#) guide to learn about core workflows and capabilities.


## Install Continuous Delivery for PE from the PE 2018.1.8+ console

If you're running PE version 2018.1.8 or a newer version in the 2018.1 series, install Continuous Delivery for PE by running a pre-populated task in the PE console.

### About this task

### Procedure

1.  In the PE console, click **Integrations**.

2.  In the **Continuous Delivery for Puppet Enterprise** area, click **Install**. The Run a task page opens with the `pe_installer_cd4pe::install` task pre-populated.

3.  In the **Value** field for the required `cd4pe_admin_email` parameter, enter the email address for the Continuous Delivery for PE administrator, also known as the root user. This email address is used as your username when signing in to Continuous Delivery for PE as the root user.

4.  In the **Value** field for the required `cd4pe_admin_password` parameter, enter a strong password for the Continuous Delivery for PE administrator account.

5.  **Optional.** Customize your Continuous Delivery for PE installation by setting any of the parameters listed in [Advanced configuration options](install_module.md#). If none of these parameters are set, your installation proceeds with the default settings.

    **Important:** In order to use the default installation, your Puppet certificate name must be a resolvable DNS hostname. If that is not the case, you must set the `resolvable_hostname` parameter to a resolvable address where the Continuous Delivery for PE server is reachable. You can use the `${trusted[certname]}` fact to set this parameter.

6.  In the **Select targets** area, specify the node on which you want to install Continuous Delivery for PE.

    **Note:** This node must be running Enterprise Linux 7, and must have Puppet installed.

7.  Click **Run Job**.

8.  When the job is complete, navigate to the URL printed on the task page. Click **Trial Mode** to start a free seven-day trial. Once this period is complete, you'll be prompted to generate and upload a license. See [Generate a license](generating_a_license.md) for instructions on creating a free 30-day trial license.


### Result:

Now that Continuous Delivery for PE is installed, [create your individual user account](create_a_user_account.md) and then move on to these next steps:

-   [Integrate your source control system.](integrations.md#)

-   [Integrate with Puppet Enterprise.](integrate_with_puppet_enterprise.md#)

-   [Configure impact analysis.](configure_impact_analysis.md#)

-   [Configure job hardware](configure_job_hardware.md#), which is used when testing your Puppet code.

-   Follow our [Getting started with Continuous Delivery for PE](getting_started.md#) guide to learn about core workflows and capabilities.


## Advanced configuration options

Customize your Continuous Delivery for PE installation from the PE console by setting any of the following parameters. If none of these parameters are set, your installation proceeds with the default settings.

|Parameters to configure the Docker image and version|
|----------------------------------------------------|
|The following two parameters are concatenated by the `puppetlabs-cd4pe` module as follows: `image => "${cd4pe_image}:${cd4pe_version}",`|
|cd4pe\_image|Set this parameter if you use an internal Docker registry for mirroring containers. Use this parameter to set the image name; use cd4pe\_version to set a tag.|
|cd4pe\_version|Use this parameter to specify a particular version of the Continuous Delivery for PE Docker container. Default is `latest`.|

|Parameters to configure the database|
|------------------------------------|
|By default, the `puppetlabs-cd4pe` module \(version 1.3.0 and newer\) creates a new installation of PE-PostgreSQL on the node where you installed Continuous Delivery for PE. If you prefer to use Amazon DynamoDB or MySQL, set the parameters in this section. CAUTION:

Changing any of these parameters post-install creates a new database and destroys all data kept in the previous database.

|
|manage\_database|Set this parameter to false to use an external DynamoDB or MySQL server. Set this parameter to true to use Continuous Delivery for PE-managed PostgreSQL or MySQL.

|
|db\_provider|Enter mysql if you're using MySQL. Do not set this parameter if using DynamoDB.|
|db\_host|Enter the address of the database. \(Required for external MySQL and DynamoDB.\)|
|db\_name|Enter the name of the database. \(Required for external MySQL and DynamoDB.\)|
|db\_pass|Enter the password for the database. \(Required for external MySQL and DynamoDB.\) CAUTION:

To set your password successfully, you must set the root\_password parameter to `Sensitive` in Hiera. For instructions, see [Setting sensitive parameters in Hiera](configure_classification.md#).

|
|db\_port|**Optional.** Enter the port the database listens on.|
|db\_prefix|**Optional.** If you'd like your database tables to share a prefix, such as `cdpe-`, enter it here.|

|Parameters to configure the port mappings|
|-----------------------------------------|
|agent\_service\_port|Defaults to 7000.|
|backend\_service\_port|Defaults to 8000.|
|web\_ui\_port|Defaults to 8080.|

|Other optional parameters|
|-------------------------|
|cd4pe\_docker\_extra\_params|To pass any additional arguments to the Docker process running the Continuous Delivery for PE container, specify them as an array.|
|analytics|To opt out of analytics data collection, set this parameter to false. To learn about what data we collect, see [Analytics data collection](cd_analytics.md#).|

## Automate upgrades of your Continuous Delivery for PE console installation

New versions of Continuous Delivery for Puppet Enterprise \(PE\) are released regularly. Automating upgrades ensures you're always taking advantage of the latest features, fixes, and improvements.

### About this task

Install the `puppetlabs-cd4pe` module, then create and classify a node group to automate management of your Continuous Delivery for PE installation's version.

### Procedure

1.  Add the module and its dependencies to your Puppetfiles.

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

2.  Add the eight modules listed above to the Puppetfile for each environment against which your compilers compile catalogs.

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

3.  Deploy the `puppetlabs-cd4pe` module and its dependencies to the `production` environment by running `puppet code deploy production`.

4.  In the PE console, click **Classification**. Click **Add group** and create a new node group with the following specifications.

    -   **Parent name**: PE Infrastructure

    -   **Group name**: Continuous Delivery for PE

    -   **Environment**: production

    -   **Environment group**: Do not select this option

5.  Open the newly created Continuous Delivery for PE node group. Add your Continuous Delivery for PE host server to the node group by either creating a rule or pinning the node.

6.  Click Configuration. In the **Add new class** field, select the **cd4pe** class and click **Add class**.

    **Note:** If the cd4pe class isn't available, click **Refresh** to update the available class definitions.

7.  To automate upgrades of Continuous Delivery for PE to the latest available version, set the `cd4pe_version` parameter to `latest`. Add the parameter and commit your change.


### Result:

After the classification change is applied on the next Puppet run, your Continuous Delivery for PE installation automatically upgrades itself whenever a new version is available.

