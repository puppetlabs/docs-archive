# Generate a custom Diffie-Hellman parameter file

The "Logjam Attack" \(CVE-2015-4000\) exposed several weaknesses in the Diffie-Hellman \(DH\) key exchange. To help mitigate the "Logjam Attack," PE ships with a pre-generated 2048 bit Diffie-Hellman param file. In the case that you don't want to use the default DH param file, you can generate your own.

## About this task

**Note:** In the following procedure, `<PROXY-CUSTOM-dhparam>.pem` can be replaced with any file name, except `dhparam_puppetproxy.pem`, as this is the default file name used by PE.

## Procedure

1.  On the console node, \(for a mono install, this is the same node as a the Puppet master\), run the following command:

    ```
    /opt/puppetlabs/puppet/bin/openssl dhparam -out /etc/puppetlabs/nginx/<PROXY-CUSTOM-dhparam>.pem 2048
    		   
    ```

    **Note:** After running this command, PE can take several minutes to complete this step.

2.  On the master, open your `pe.conf` file \(located at `/etc/puppetlabs/enterprise/conf.d/pe.conf`\) and add the following parameter and value:

    ```
    "puppet_enterprise::profile::console::proxy::dhparam_file": "/etc/puppetlabs/nginx/<PROXY-CUSTOM-dhparam>.pem"
    
    ```

3.  On the console node, run Puppet: `puppet agent -t`.


