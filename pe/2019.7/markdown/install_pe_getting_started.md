# Install PE

Installing PE sets up a standard installation, which you can use to try out PE with up to 10 nodes or to manage up to 4,000 nodes.

Ensure your system capacity can manage a PE installation by reviewing the [hardware requirements for standard installations](hardware_requirements.md#).

A standard PE installation consists of these components installed on a single node:

-   The master: the central hub of activity, where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed.
-   The console: the graphical web interface, which features configuration and reporting tools.
-   PuppetDB: the data store for data generated throughout your Puppet infrastructure.

**Important:** The Puppet master runs only on \*nix machines. Windows machines can run as Puppet agents, and you can manage them with your \*nix Puppet master. If you want to operate a \*nix master remotely from a Windows machine, before installing PE, configure an SSH client, such as [PuTTY](https://putty.org/), with the hostname or IP address and port of the \*nix machine that you want to install as your master. When you open an SSH session to install the \*nix master, log in as the `root` user.

## Install PE

Installation uses default settings to install all of the PE infrastructure components on a single node. After installing, you can scale or customize your installation as needed.

1.  On your master, log in as root and [download](https://puppet.com/try-puppet/puppet-enterprise/download/) the tarball appropriate to your operating system and architecture.

    **Tip:** To download packages from the command line, run `wget --content-disposition <URL>` or `curl -JLO <URL>`, using the URL for the tarball you want to download.

2.  Unpack the installation tarball:

    ```
    tar -xf <TARBALL_FILENAME>              
    ```

3.  From the installer directory, run the installer:

    ```
    sudo ./puppet-enterprise-installer
    ```

4.  When prompted, specify a console administrator password: `puppet infrastructure console_password --password=<MY_PASSWORD>`

5.  Run Puppet twice: `puppet agent -t`.


You must restart the shell before you can use client tool commands.

## Log into the PE console

The console is a graphical interface where you can manage your infrastructure without relying on the command line.

To log in for the first time:

1.  Open the console by entering the URL <master\_certname\> into your browser, where master\_certname is the trusted certificate name of your master.

    **Note:** You'll receive a browser warning about an untrusted certificate because you were the signing authority for the console's certificate, and your Puppet Enterprise deployment is not known to your browser as a valid signing authority. Ignore the warning and accept the certificate.

2.  On the login page for the console, log in with the user name `admin` and the password you created when installing. Keep track of this login as you will use it later.


Next, check the status of your PE master.

## Check the status of your master

You can run a task to check the status of your master in the console.

A task is a single action that allows you to do ad-hoc things like upgrade packages and restart services on target machines. PE comes with a few tasks installed, such as `package`, `service`, and `puppet_conf`, and you can download more tasks from the Forge or write your own.

1.  In the console, in the Run section, click **Task**.

2.  In the **Task** field, select `service` because you are checking the status of the master service.

3.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

4.  Set parameters and values for the task. The `service` task has two required parameters. For **action**, choose `status`. For **name**, enter puppet.

5.  Under **Select targets**, choose **Node list**.

    1.  In the **Inventory nodes** field, add the hostname of your master and select it.

6.  Click **Run job**.


View the task status and output in the **Jobs** page when it is finished running.

The status of your master should be “running” and “enabled”. To learn more about tasks, including how to install them from the Forge or how to write your own, visit the [Installing tasks](installing_tasks.md#) and [Writing tasks](writing_tasks.md#) sections of the docs.

Next, use the console to add nodes to your inventory.

