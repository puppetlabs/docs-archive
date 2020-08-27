---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Install Continuous Delivery for PE using the `cd4pe` module

Use version 1.1.0 or later of the `puppetlabs-cd4pe` module to install and configure Continuous Delivery for Puppet Enterprise \(PE\). This module installs Docker, configures the Continuous Delivery for PE Docker image and service for you, and creates a Docker volume for disk storage. You can also use the module to configure your Continuous Delivery for PE root account and to manage a MySQL container.

CAUTION:

Code Manager webhooks are not compatible with Continuous Delivery for PE. If your organization currently uses Code Manager webhooks to deploy code, you must dismantle these webhooks before installing Continuous Delivery for PE.

## Install Continuous Delivery for PE with the `cd4pe` module

Use the puppetlabs-cd4pe module version 1.1.0 or later to install Continuous Delivery for PE.

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

3.  In the PE console, click **Classification**. Click **Add group** and create a new node group with the following specifications.

    -   **Parent name**: PE Infrastructure

    -   **Group name**: Continuous Delivery for PE

    -   **Environment**: production

    -   **Environment group**: Do not select this option

4.  Open the newly created Continuous Delivery for PE node group. Add the server you wish to use as your Continuous Delivery for PE host server to the node group by either creating a rule or pinning the node.

5.  Click Configuration. In the **Add new class** field, select the **cd4pe** class and click **Add class**.

    **Note:** If the cd4pe class isn't available, click **Refresh** to update the available class definitions.

6.  To automate upgrades of Continuous Delivery for PE to the latest available version, set the `cd4pe_version` parameter to `latest`. Add the parameter and commit your change.

    After the classification change is applied on the next Puppet run, your Continuous Delivery for PE installation automatically upgrades itself whenever a new version is available.

7.  **Optional.** Customize your Continuous Delivery for PE installation by setting any of the parameters listed in [Advanced configuration options](install_module.md#). If none of these parameters are set, your installation proceeds with the default settings.

    **Important:** In order to use the default installation, your Puppet certificate name must be a resolvable DNS hostname. If that is not the case, you must set the `resolvable_hostname` parameter to a resolvable address where the Continuous Delivery for PE server is reachable. You can use the `${trusted[certname]}` fact to set this parameter.

8.  Run Puppet on your Continuous Delivery for PE host server.

    **Note:** Once the Puppet agent run is complete, Docker downloads the Continuous Delivery for PE image, which can take up to a few minutes.


### Result:

Continuous Delivery for PE is now installed. Next, complete the configuration of the software.

## Configure Continuous Delivery for PE with a task

Once you've completed the installation of Continuous Delivery for PE using the `cd4pe` module, run a task to configure the software.

### About this task

**Tip:** If you prefer to use classification, rather than a task, to configure your Continuous Delivery for PE installation, see [Configure a Continuous Delivery for PE module installation using classification](configure_classification.md#).

### Procedure

1.  In the PE console, click **Classification**. Expand the **PE Infrastructure** node group and click **Continuous Delivery for PE**.

2.  Click **Run** and select **Task**.

3.  In the **Task** field, select `cd4pe::root_configuration`.

4.  Enter parameters for the task, as follows:

    |Parameter|Value|Notes|
    |---------|-----|-----|
    |root\_email|The email address to associate with the root account.|Required.|
    |root\_password|The password to associate with the root account.|Required.|
    |resolvable\_hostname|The resolvable hostname where the Continuous Delivery for PE container can be reached. For example, if the container resides on a Docker host named `mydockerengine.myinc.com` , set resolvable\_hostname to `http://mydockerengine.myinc.com`.

|Required only if the agent certificate is not the machine's resolvable internet address.|
    |agent\_service\_endpoint|The endpoint where the agent service can be reached, in the form `http://<resolvable_hostname>:<port>`.|Required if you set the agent\_service\_port parameter in the cd4pe class during installation.|
    |backend\_service\_endpoint|The endpoint where the back end service can be reached, in the form `http://<resolvable_hostname>:<port>`.|Required if you set the backend\_service\_port parameter in the cd4pe class during installation.|
    |web\_ui\_endpoint|The endpoint where the web UI can be reached, in the form `http://<resolvable_hostname>:<port>`.|Required if you set the web\_ui\_port parameter in the cd4pe class during installation.|
    |storage\_provider|Which object store provider to use. Must be one of: DISK, ARTIFACTORY or S3.|Defaults to DISK.|
    |storage\_bucket|The name of the bucket used for object storage.|Required if using Amazon S3 or Artifactory for object storage.|
    |storage\_endpoint|The URL of the storage provider.|Required if using Amazon S3 or Artifactory for object storage.|
    |storage\_prefix|For Amazon S3: the subdirectory of the bucket to use. For Artifactory: the top level of the Artifactory instance.

|Optional.|
    |s3\_access\_key|The AWS access key that has access to the bucket.|Required if using Amazon S3.|
    |s3\_secret\_key|The AWS secret key that has access to the bucket.|Required if using Amazon S3.|
    |artifactory\_access\_token|API token for your Artifactory instance.|Required if using Artifactory.|

5.  Click **Run job**.

6.  When the job is complete, navigate to the URL printed on the task page. Click **Trial Mode** to start a free seven-day trial. Once this period is complete, you'll be prompted to generate and upload a license. See [Generate a license](generating_a_license.md) for instructions on creating a free 30-day trial license.


### Result:

Now that Continuous Delivery for PE is installed and configured, [create your individual user account](create_a_user_account.md) and then move on to these next steps:

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

