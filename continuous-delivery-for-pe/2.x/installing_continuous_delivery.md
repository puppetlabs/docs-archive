---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Install Continuous Delivery for PE using Docker

Installing Continuous Delivery for Puppet Enterprise \(PE\) requires you to pull and run a Docker container, then follow our guided setup to create the root account and configure your installation.

## Before you begin

Review the [system requirements](cd_system_requirements.md#) and ensure you have:

-   The ability to run a [Docker](https://forge.puppet.com/puppetlabs/docker) image.

**Note:** Some operating systems ship with older versions of Docker that do not work well with Continuous Delivery for PE. For best results, install [Docker Community Edition \(CE\)](https://docs.docker.com/install/) or [Docker Enterprise Edition \(EE\)](https://docs.docker.com/ee/supported-platforms/) using packages distributed directly by Docker.

-   A database for persisting information, either [MySQL](https://forge.puppet.com/puppetlabs/mysql) version 5.7 or [Amazon DynamoDB](https://aws.amazon.com/dynamodb/getting-started/).

**Note:** Your MySQL database must use the `latin1` character set and `latin1_swedish_ci` collation.


CAUTION:

Code Manager webhooks are not compatible with Continuous Delivery for PE. If your organization currently uses Code Manager webhooks to deploy code, you must dismantle these webhooks before installing Continuous Delivery for PE.

## Procedure

1.  Continuous Delivery for PE is delivered as a Docker container. Pull down the latest version by running:

    ```no-highlight
    docker pull puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

2.  Run the image with the command appropriate to your database infrastructure, replacing the options with the appropriate values, as per the instructions below.

    **For MySQL:**

    ```no-highlight
    docker run --rm --name cd4pe -v cd4pe:/disk -e DB_ENDPOINT=<MY_DATABASE_ENDPOINT> -e DB_USER=<MY_DB_USERNAME> -e DB_PASS=<MY_DB_PASS> -e DB_PREFIX=<OPTINAL_PREFIX_TO_DB_TABLES> -e DUMP_URI=<DUMP_URI> -e PFI_SECRET_KEY=<KEY_FOR_DB_SECRETS_ENCRYPTION> -p 8080:8080 -p 8000:8000 -p 7000:7000 puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

    **For Amazon DynamoDB:**

    ```no-highlight
    docker run --rm --name cd4pe -v cd4pe:/disk -e DB_ENDPOINT=ddb://<AWS_REGION> -e DB_USER=<AWS_ACCESS_KEY> -e DB_PASS=<AWS_SECRET_KEY> -e DUMP_URI=<DUMP_URI> -e PFI_SECRET_KEY=<KEY_FOR_DB_SECRETS_ENCRYPTION> -p 8080:8080 -p 8000:8000 -p 7000:7000 puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

    -   **`--name`**

        A friendly name you give to the running image. We used `cd4pe` in the example above.

    -   **`-v cd4pe:/disk`**

        Creates a new Docker volume called `cd4pe` for the user, which will be used for storing logs, job manifests, and artifacts. To learn more about this Docker volume, use the command `docker volume inspect cd4pe`. To learn more about Docker volumes in general, see the [Docker documentation](https://docs.docker.com/storage/volumes/).

        **Important:** If you wish to use Amazon S3 or Artifactory as an external storage provider, do not include this option in your `docker run` command.

    -   **`DB_ENDPOINT`**

        The `mysql://` or `ddb://` endpoint used to connect to your database. For example, `mysql://samplehost:3306/cdpe`

    -   **`DB_USER` and `DB_PASS`**

        **For MySQL:** Login credentials for your MySQL user. For security purposes, the database user you select should be able to connect to only this database.

        **For Amazon DynamoDB:** Access and secret keys for your DynamoDB user.

        **Important:** Make sure you generate credentials with full create, read, update, and delete permissions for DynamoDB resources.

        For security purposes, select a database user you select who can connect to only this database.

    -   **`DB_PREFIX`**

        When starting up, Continuous Delivery for PE creates tables in MySQL or DynamoDB. If you'd like the tables to share a prefix, such as `cdpe-`, enter it here.

        **Tip:** If you wish to simulate a fresh installation of a given version of Continuous Delivery for PE, entering a new database table prefix causes all the database tables to regenerate. 

    -   **`DUMP_URI`**

        How to address port 7000 of this container, which is the endpoint used by the Continuous Delivery for PE web UI to connect to the correct instance of the Continuous Delivery agent service. In a typical installation, this value is `dump://localhost:7000`.

    -   **`PFI_SECRET_KEY`**

        A 16-byte secret key used for AES encryption of secrets \(such as PE access tokens\) supplied to Continuous Delivery for PE.

        If you're a \*nix user, generate this key by running:

        ```no-highlight
        dd bs=1 if=/dev/urandom count=16 2>/dev/null | base64
        ```

        If you're a Windows user, generate this key by running:

        ```no-highlight
        $randomBytes = New-Object Byte[](16)
        [Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($randomBytes) 
        $encodedBytes = [System.Convert]::ToBase64String($randomBytes)
        
        ```

    -   **`-p 8080:8080 -p 8000:8000 -p 7000:7000`**

        The three ports used by Continuous Delivery for PE for the web UI \(8080\), the backend service \(8000\), and the agent service \(7000\). You can map these ports to any port of your choosing.

3.  Point your web browser to `localhost:8080` to access the Continuous Delivery for PE web UI. Click **Get Started**.

4.  Follow the prompts to configure your installation. You'll be asked to:

    1.  Select the email address and password to associate with the root account.

        **Note:** The root account is used for installing and configuring your organization's Continuous Delivery for PE instance.

    2.  Configure the endpoints for Continuous Delivery for PE services.

        **Important:** For production-grade systems supporting multiple Continuous Delivery for PE users, configure these services to point to the DNS endpoints of load balancers. This is especially vital if you plan to run multiple container replicas of Continuous Delivery for PE.

    3.  If desired, integrate an external storage provider \(Amazon S3 or Artifactory\) by adding credentials. You can skip this step if you are using disk storage.

    4.  Click **Trial Mode** to start a free seven-day trial. Once this period is complete, you'll be prompted to generate and upload a license. See [Generate a license](generating_a_license.md) for instructions on creating a free 30-day trial license.

5.  When configuration is complete, you'll see a success message. Click **Create your user account** to sign out of the root account.

    **Tip:** If your organization uses GitHub, return to the root console and set up your GitHub OAuth application before signing out as root. See [Integrate with GitHub](integrations.md#) for instructions.


## Result:

Now that Continuous Delivery for PE is installed, create your individual user account and move on to these next steps:

-   [Integrate your source control system.](integrations.md#)

-   [Integrate with Puppet Enterprise.](integrate_with_puppet_enterprise.md#)

-   [Configure impact analysis.](configure_impact_analysis.md#)

-   [Configure job hardware](configure_job_hardware.md#), which is used when testing your Puppet code.

-   Follow our [Getting started with Continuous Delivery for PE](getting_started.md#) guide to learn about core workflows and capabilities.


