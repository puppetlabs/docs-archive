---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Regenerate Windows agent certificates

Regenerate Windows agent certificates to fix a compromised certificate or troubleshoot SSL errors on agents, or if you recreated your certificate authority.

Unless otherwise indicated, perform these steps on the Windows agent node that you're regenerating certificates for.

1.  If you did not recreate your certificate authority, you must log into your master and clear the cert for the agent node:

    ```
    puppetserver ca clean --certname <CERTNAME>
    ```

2.  On the agent, back up the `%PROGRAMDATA%/PuppetLabs/puppet/etc/ssl` directory.

    If something goes wrong, you might need to restore these directories so your deployment remains functional.

3.  Stop the Puppet agent and PXP agent services.

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pxp-agent ensure=stopped
    ```

4.  Using the administrator account, delete the agent SSL directory located at `%PROGRAMDATA%/PuppetLabs/puppet/etc/ssl`.

5.  Remove the agent's cached catalog. Use the Administrator confdir to delete `%PROGRAMDATA%\PuppetLabs\puppet\cache\client_data\catalog\<CERNAME>.json`.

6.  Re-start the Puppet agent service: `puppet resource service puppet ensure=running`

    After the agent starts, it automatically generates keys and request a new certificate from the Puppet CA.

7.  If you aren't using autosigning, sign each agent node's certificate request using the console's request manager, or from your master:

    ```
    puppetserver ca list 
    puppetserver ca sign --certname <NAME>
    ```

    **Note:** For more information about autosigning, see [Autosigning certificate requests](https://puppet.com/docs/puppet/6.10/ssl_autosign.html).

8.  From the console or command line, run Puppet on the node.


The Windows agent performs a full catalog run, restarts the PXP agent service, and resumes its role in your PE deployment.

