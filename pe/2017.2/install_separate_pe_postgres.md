---
layout: default
title: "Standalone PE-PostgreSQL Installation"
canonical: "/pe/latest/install_separate_pe_postgres.html"
---

In a default PE installation, PE-PostgreSQL (the database backend for PE ) is installed alongside PuppetDB. If needed, you can install a separate, standalone instance of PE-PostgreSQL.

Before you begin, please read the following:

To install a standalone PE-PostgreSQL node, you'll need root access to the node, and you'll need the ability to SSH and copy files over to it.

>**NOTE:** If you do not have root access, or you want to use a dedicated database as a service tool, such as Amazon's RDS, refer to, [Enabling PE and external PostgreSQL without root access](./install_external_postgresql.html)

This example is based on a monolithic installation. We'll refer to the the master of masters (MoM) as `pe-mom.example.com` and to the PE-PostgreSQL node as `pe-postgres.example.com`.

>**Important:** You can only install a standalone PE-PostrgreSQL node using text mode installation.

1. Refer to the text mode installation instructions for a [monolithic PE install](./install_text_mode_mono.html).
2. When editing the `pe.conf` file for the installation, specify the following parameters:

   ```
  "puppet_enterprise::puppet_master_host": "pe-mom.example.com"
  "puppet_enterprise::console_admin_password": "YOUR CONSOLE ADMIN PASSWORD"
  "puppet_enterprise::database_host": "pe-postgres.example.com"
   ```

3. Run the installer with the `pe.conf` file you created.

   ```
   sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
   ```

The installer will fail halfway through, as it cannot contact the database, and the PE components that rely on the database (PuppetDB, PE Console) will fail to start. The next installation run will correct this.

4. Copy the `pe.conf` you made to the `pe-postgres.example.com` and SSH into that node.
5. Run the installer with the same `pe.conf`.

  ```
  sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
  ```

6. When the installation process completes on the `pe-postgres.example.com` node, SSH back into the `pe-mom.example.com` node, and run the following commands to complete the installation:

  ```
  puppet infrastructure configure;
  puppet agent -t;
  ```

After these Puppet runs, the MoM is configured to use the standalone PE-PostgreSQL installation on `pe-postgres.example.com`.
