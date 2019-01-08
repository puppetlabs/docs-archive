---
layout: default
title: "Uninstalling Razor"
canonical: "/pe/latest/razor_uninstall.html"

---

To uninstall the Razor server:

1. Run `yum erase pe-razor`.
2. Drop the PostgreSQL database that the server used.
3. Change DHCP/TFTP so that the machines that have been installed will continue to boot outside the scope of Razor.

To uninstall the Razor client:

+  Run `gem uninstall pe-razor-client`.


* * *
