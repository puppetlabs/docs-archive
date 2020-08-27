---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Analytics data collection

Continuous Delivery for Puppet Enterprise \(PE\) automatically collects data about how you use the software. If you want to opt out of providing this data, you can do so when installing Continuous Delivery for PE.

Continuous Delivery for PE collects analytics data in order to better understand how our customers are using the software. For example, knowing how many control repos you manage helps us develop more realistic product testing. And learning which source control systems are the most and the least used helps us decide where to prioritize new functionality.

## What data does Continuous Delivery for PE collect?

Continuous Delivery for PE collects analytics data when you use the software. No personally identifiable information is collected, and the data we collect is never used or shared outside Puppet.

The following data is collected when the software starts for the first time or restarts after an upgrade, and once per week thereafter:

-   License UUID

-   For each active user account:

-   Number of control repos

-   Number of control repo pipelines

-   Number of control repo pipelines with impact analysis enabled

-   Number of modules

-   The deployment policy selected for each pipeline deployment

-   Configured integrations \(PE and source control systems\)

The following data is collected while Continuous Delivery for PE is in use:

-   Pageviews

-   Progress through the initial installation and setup process

-   Progress through the integration configuration process \(PE and source control systems\)


Continuous Delivery for PE **does not** collect:

-   Any personally identifiable information about a user, such as name, email address, password, or company name

-   User inputs such as usernames, control repo names, module names, job names, or job hardware information


## Opt out of analytics data collection when installing with the `cd4pe` module

To opt out of analytics data collection, use the PE console to set the `analytics` parameter on the `cd4pe` class.

### About this task

### Procedure

1.  Follow steps 1 through 5 of the installation instructions in [Install Continuous Delivery for PE with the](install_module.md#)`cd4pe`module.

2.  On the **Configuration** page of the Continuous Delivery for PE node group, add the **analytics** parameter to the **cd4pe** class. Set the parameter's value to false.

3.  Run Puppet on your Continuous Delivery for PE node group to apply the change.

    You have opted out of data collection, and your new instance of Continuous Delivery for PE will not send analytics data to Puppet.


## Opt out of analytics data collection when installing with Docker

To opt out of analytics data collection, set the `ANALYTICS` environment variable to `false` during the initial Docker run of the Continuous Delivery for Puppet Enterprise container.

### About this task

### Procedure

1.  Pull down the latest version of the Docker container by running:

    ```
    docker pull puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

2.  Follow the instructions in step 2 of [Install Continuous Delivery for PE](installing_continuous_delivery.md) to run the image appropriate to your database infrastructure, adding `-e ANALYTICS=false` as an environment variable.

    For MySQL:

    ```
    docker run --rm --name cd4pe -v cd4pe:/disk -e DB_ENDPOINT=<MY_DATABASE_ENDPOINT> -e DB_USER=<MY_DB_USERNAME> -e DB_PASS=<MY_DB_PASS> -e DB_PREFIX=<OPTINAL_PREFIX_TO_DB_TABLES> -e DUMP_URI=<DUMP_URI> -e PFI_SECRET_KEY=<KEY_FOR_DB_SECRETS_ENCRYPTION> -e ANALYTICS=false -p 8080:8080 -p 8000:8000 -p 7000:7000 puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

    For Amazon DynamoDB:

    ```
    docker run --rm --name cd4pe -v cd4pe:/disk -e DB_ENDPOINT=ddb://<AWS_REGION> -e DB_USER=<AWS_ACCESS_KEY> -e DB_PASS=<AWS_SECRET_KEY> -e DUMP_URI=<DUMP_URI> -e PFI_SECRET_KEY=<KEY_FOR_DB_SECRETS_ENCRYPTION> -e ANALYTICS=false -p 8080:8080 -p 8000:8000 -p 7000:7000 puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

3.  Continue with the installation process as described in the documentation.

    You have opted out of data collection, and your new instance of Continuous Delivery for PE will not send analytics data to Puppet.


