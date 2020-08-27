---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configure a Continuous Delivery for PE module installation using classification

Once you've completed the installation of Continuous Delivery for PE using the `cd4pe` module, you can use classification to configure the software.

## Procedure

1.  In the PE console, click **Classification**. Expand the **PE Infrastructure** node group and click **Continuous Delivery for PE**.

2.  Click **Configuration**. In the **Add new class** field, select **cd4pe::root\_config**.

3.  Enter parameters for the class, as follows:

    |Parameter|Value|Notes|
    |---------|-----|-----|
    |root\_email|The email address to associate with the root account.|Required.|
    |root\_password|The password to associate with the root account. CAUTION:

To set your password successfully, you must set the root\_password parameter to `Sensitive` using Hiera. This parameter cannot be set successfully in the PE console. For instructions, see [Setting sensitive parameters in Hiera](configure_classification.md#).

|Required. **Note:** If you need to update the root account password, first do so in the Continuous Delivery for PE web UI, and then update the password in the PE console using this parameter.

|
    |storage\_provider|Which object store provider to use. Must be one of: DISK, ARTIFACTORY or S3.|Defaults to DISK.|
    |storage\_bucket|The name of the bucket used for object storage.|Required if using Amazon S3 or Artifactory for object storage.|
    |storage\_endpoint|The URL of the storage provider.|Required if using Amazon S3 or Artifactory for object storage.|
    |storage\_prefix|For Amazon S3: the subdirectory of the bucket to use. For Artifactory: the top level of the Artifactory instance.

|Optional.|
    |s3\_access\_key|The AWS access key that has access to the bucket.|Required if using Amazon S3.|
    |s3\_secret\_key|The AWS secret key that has access to the bucket.|Required if using Amazon S3.|
    |artifactory\_access\_token|API token for your Artifactory instance.|Required if using Artifactory.|

4.  Commit your changes and run Puppet on the node group.

5.  When the Puppet run is complete, navigate to port 8080 of the Continuous Delivery for PE host server. Click **Trial Mode** to start a free seven-day trial. Once this period is complete, you'll be prompted to generate and upload a license. See [Generate a license](generating_a_license.md) for instructions on creating a free 30-day trial license.


## Result:

Now that Continuous Delivery for PE is installed and configured, [create your individual user account](create_a_user_account.md) and then move on to these next steps:

-   [Integrate your source control system.](integrations.md#)

-   [Integrate with Puppet Enterprise.](integrate_with_puppet_enterprise.md#)

-   [Configure impact analysis.](configure_impact_analysis.md#)

-   [Configure job hardware](configure_job_hardware.md#), which is used when testing your Puppet code.

-   Follow our [Getting started with Continuous Delivery for PE](getting_started.md#) guide to learn about core workflows and capabilities.


## Setting sensitive parameters in Hiera

When passing sensitive information, such as the root password for Continuous Delivery for PE, as parameters, set the value of these parameters as `Sensitive` in Hiera.

To set the value of parameters from `String` to `Sensitive` in Hiera, add the parameters to a `lookup_options` section in the `common.yaml` file. For example:

```
---
lookup_options:
  '^cd4pe::db_pass$':
    convert_to: 'Sensitive'
  '^cd4pe::root_config::root_password$':
    convert_to: 'Sensitive'
  '^cd4pe::root_config::s3_secret_key$':
    convert_to: 'Sensitive'
  '^cd4pe::root_config::artifactory_access_token$':
    convert_to: 'Sensitive'  

```

