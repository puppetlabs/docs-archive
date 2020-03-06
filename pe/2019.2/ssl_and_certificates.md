---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# SSL and certificates

Network communications and security in Puppet Enterprise are based on HTTPS, which secures traffic using X.509 certificates. PE includes its own CA tools, which you can use to regenerate certs as needed.

-   **[Regenerate certificates](regenerate_certificates.md#)**  
Regenerating certificates and security credentials—both private and public keys—created by the built-in PE certificate authority can help ensure the security of your installation in certain cases.
-   **[Use an independent intermediate certificate authority](use_an_independent_intermediate_ca.md)**  
The built-in Puppet certificate authority automatically generates a root and intermediate certificate, but you can set up an independent intermediate certificate authority during installation if you need additional intermediate certificates, or if you prefer to use a public authority CA.
-   **[Use a custom SSL certificate for the console](use_a_custom_ssl_cert_for_the_console.md)**  
The console uses a certificate signed by PE's built-in certificate authority \(CA\). Because this CA is specific to PE, web browsers don't know it or trust it, and you have to add a security exception in order to access the console. You might find that this is not an acceptable scenario and want to use a custom CA to create the console's certificate.
-   **[Change the hostname of a master](change_hostname_monolithic_master.md)**  
To change the hostnames assigned to your PE infrastructure nodes requires updating the corresponding PE certificate names. You might have to update your master hostname, for example, when migrating to a new PE installation, or after an organizational change such as a change in company name.
-   **[Generate a custom Diffie-Hellman parameter file](generate_custom_dh_parameter_file.md)**  
The "Logjam Attack" \(CVE-2015-4000\) exposed several weaknesses in the Diffie-Hellman \(DH\) key exchange. To help mitigate the "Logjam Attack," PE ships with a pre-generated 2048 bit Diffie-Hellman param file. In the case that you don't want to use the default DH param file, you can generate your own.
-   **[Enable TLSv1](enable_tlsv1.md)**  
TLSv1 and TLSv1.1 are disabled by default in PE.

