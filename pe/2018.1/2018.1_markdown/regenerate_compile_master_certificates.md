# Regenerate compile master certs

From time to time, you may encounter a situation in which you need to regenerate a certificate for a compile master. Perhaps there is a security vulnerability in your infrastructure that you can remediate with a certificate regeneration, or maybe you're receiving strange SSL errors on your compile master that are preventing you from performing normal operations.

## About this task

Unless otherwise indicated, the following steps are performed on your compile master nodes.

## Procedure

1.  Log into the master of masters \(MoM\) as `root`.

2.  On the MoM, run `puppet cert clean <COMPILE MASTER HOSTNAME>`.

3.  Log into the compile master node as `root`.

4.  Back up the `/etc/puppetlabs/puppet/ssl/` directory. Run `cp -r /etc/puppetlabs/puppet/ssl/ /etc/puppetlabs/puppet/ssl_bak/`.

    If something goes wrong, you can restore this directory to keep your deployment functioning.

5.  Stop the Puppet agent, MCollective, and PXP agent services.

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pe-puppetserver ensure=stopped
    puppet resource service mcollective ensure=stopped
    puppet resource service pxp-agent ensure=stopped			 
    ```

6.  Delete the compile master's SSL directory. Run `rm -rf /etc/puppetlabs/puppet/ssl`.

7.  Remove the compile master's cached catalog. Run `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERT NAME>.json`.

8.  Re-start the Puppet agent service and manually trigger a Puppet run, or wait for the next automatically scheduled run. 

    ```
    puppet resource service puppet ensure=running		 
    ```

9.  Log into the MoM as `root`.

10. On the MoM, sign the compile master's certificate request. Run `puppet cert --allow-dns-alt-names sign <compile master hostname>`.

11. Log into the compile master as `root`.

12. Run Puppet.

    PE performs a full catalog run, and the compile master will resume its role in your PE deployment.


