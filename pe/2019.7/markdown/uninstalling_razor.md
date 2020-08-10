---
author: melissa amos <melissa.amos@puppet.com\>
---

# Uninstalling Razor

If you're permanently done provisioning nodes, you can uninstall Razor.

## Uninstall the Razor server

To uninstall the Razor server, erase Razor from the server node and update your environment so that nodes continue to boot as expected.

1.  On the node with the Razor server, run `yum erase pe-razor`

2.  Drop the PostgreSQL database that the server used.

3.  Change DHCP/TFTP so that the machines that have been installed continue to boot outside the scope of Razor.


## Uninstall the Razor client

Uninstall the Razor client if you no longer wish to use it to interact with a Razor server.

1.  Run `gem uninstall razor-client`.


