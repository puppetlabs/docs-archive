# Configure SSL protocols

Add or remove SSL protocols in your PE infrastructure.

To change what SSL protocols your PE infrastructure uses, use Hiera or the console to add or remove protocols.

Use the parameter `puppet_enterprise::master::puppetserver::ssl_protocols` and add an array for protocols you want to include, or remove protocols you no longer want to use.

For example, to enable TLSv1.1 and TLSv1.2, set the following parameter in the **PE Infrastructure** group in the console or in your Hiera data.

```
puppet_enterprise::master::puppetserver::ssl_protocols["TLSv1.1", "TLSv1.2"]
```

**Note:** To comply with security regulations, PE 2019.1 and later uses only version 1.2 of the Transport Layer Security \(TLS\) protocol.

