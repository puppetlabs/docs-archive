---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configure SSL

Continuous Delivery for Puppet Enterprise \(PE\) supports the use of Secure Sockets Layer \(SSL\) for enhanced security when using the software.

## SSL requirements

Before enabling SSL on your system, review the following important information.

1.  Enabling SSL requires super user permissions.
2.  After configuring SSL, you must reinstall the Continuous Delivery agent on all job hardware. For instructions, see [Configure job hardware](configure_job_hardware.md#).

3.  Enabling SSL requires a restart of the Continuous Delivery for PE Docker container.


## Setting up a new SSL configuration

Configure your Continuous Delivery for PE instance to use SSL by entering the relevant certificates in the root console and then updating your web UI endpoint to reflect the new DNS host and SSL port.

### Before you begin

Review the SSL requirements section above.

### About this task

### Procedure

1.  Log into the root console by selecting **Root Console** from the workspaces menu at the top of the Continuous Delivery for PE navigation bar or signing in as the root user.

2.  Click **Settings** and make sure you're viewing the **Endpoints** tab.

3.  In the Configure SSL area, paste in the CA certificate, server certificate, and server private key for your Continuous Delivery for PE host.

4.  Click the toggle to **Enable SSL**.

    **Note:** You can leave your SSL configuration disabled and save the information you've entered. If SSL information is entered and saved but not enabled, your certificates are saved and the private key is saved in an encrypted format until you're ready to enable SSL.

5.  Click **Save SSL Settings**. If you've enabled SSL, proceed to the next step.

6.  Stop the Continuous Delivery for PE container by running `docker stop <CONTAINER NAME>`.

7.  Restart Continuous Delivery for PE with the command appropriate to your database infrastructure, replacing the options with the appropriate values, as per the instructions below.

    **For MySQL:**

    ```no-highlight
    docker run --rm --name cd4pe -v cd4pe:/disk -e SSL_PORT=8443 -e DB_ENDPOINT=<MY_DATABASE_ENDPOINT> -e DB_USER=<MY_DB_USERNAME> -e DB_PASS=<MY_DB_PASS> -e DB_PREFIX=<OPTINAL_PREFIX_TO_DB_TABLES> -e DUMP_URI=<DUMP_URI> -e PFI_SECRET_KEY=<KEY_FOR_DB_SECRETS_ENCRYPTION> -p 8443:8443 -p 8000:8000 -p 7000:7000 puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

    **For Amazon DynamoDB:**

    ```no-highlight
    docker run --rm --name cd4pe -v cd4pe:/disk -e SSL_PORT=8443 -e DB_ENDPOINT=ddb://<AWS_REGION> -e DB_USER=<AWS_ACCESS_KEY> -e DB_PASS=<AWS_SECRET_KEY> -e DUMP_URI=<DUMP_URI> -e PFI_SECRET_KEY=<KEY_FOR_DB_SECRETS_ENCRYPTION> -p 8443:8443 -p 8000:8000 -p 7000:7000 puppet/continuous-delivery-for-puppet-enterprise:latest
    ```

    -   **`--name`**

        A friendly name you give to the running image. We used `cd4pe` in the example above.

    -   **`-v cd4pe:/disk`**

        Creates a new Docker volume called `cd4pe` for the user, which will be used for storing logs, job manifests, and artifacts. To learn more about this Docker volume, use the command `docker volume inspect cd4pe`. To learn more about Docker volumes in general, see the [Docker documentation](https://docs.docker.com/storage/volumes/).

        **Important:** If you wish to use Amazon S3 or Artifactory as an external storage provider, do not include this option in your `docker run` command.

    -   **`SSL_PORT`**

        Sets the SSL port. By default, Continuous Delivery for PE uses port 8443 for SSL.

    -   **`DB_ENDPOINT`**

        The `mysql://` or `ddb://` endpoint used to connect to your database. For example, `mysql://samplehost:3306/cdpe`

    -   **`DB_USER` and `DB_PASS`**

        **For MySQL:** Login credentials for your MySQL user. For security purposes, select a database user who can connect to only this database.

        **For Amazon DynamoDB:** Access and secret keys for your DynamoDB user.

        **Important:** Make sure you generate credentials with full create, read, update, and delete permissions for DynamoDB resources.

        For security purposes, select a database user who can connect to only this database.

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

    -   **`-p 8443:8443 -p 8000:8000 -p 7000:7000`**

        The three ports used by Continuous Delivery for PE for the web UI over SSL \(8443 by default\), the backend service \(8000\), and the agent service \(7000\). You can map these ports to any port of your choosing.

8.  In the root console, update the WebUI endpoint in the **Configure Endpoints** area. The format for the new WebUI endpoint is `https://<DNS_HOST>:<SSL_PORT>`.

9.  **Azure DevOps users:** Update the backend service endpoint to use `https`. This change allows Azure DevOps webhooks to function correctly.


### Result:

You can now access Continuous Delivery for PE over SSL by pointing your web browser to the new WebUI endpoint entered above. Access over both `https` and `http` is allowed.

Now that SSL is configured on your system, you must reinstall the Continuous Delivery agent on all job hardware. For instructions, see [Configure job hardware](configure_job_hardware.md#).

