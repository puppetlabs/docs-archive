---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configure job hardware

Job hardware, or the servers Continuous Delivery for Puppet Enterprise \(PE\) uses to run tests on your Puppet code, must be configured before you can begin running tests or using pipelines. 

**Important:** Due to a Docker known issue, running job hardware in the container where Continuous Delivery for PE is installed results in a `Failed to connect to <ADDRESS> port <PORT NUMBER>: Host is unreachable` error. To work around this issue, install your job hardware in a separate container, or update your Docker firewall settings to allow the two services to communicate.

## Selecting a job hardware installation method

There are two methods for authenticating your account when configuring job hardware. Which you choose to employ is mainly a question of how many servers you're designating as job hardware.

When installing the Continuous Delivery agent, you must enter the email address and password associated with your Continuous Delivery for PE account. Doing this manually is fine if you're setting up a server or two, but if you're configuring a larger number of servers, adding these credentials can quickly become cumbersome. In this case, using a `distelli.yml` file to automatically pass your credentials to the agent is the more efficient method.

## Configure job hardware with the web UI

To designate a server as job hardware, install the Continuous Delivery agent on the server and mark it as active job hardware in the web UI.

### About this task

**Tip:** This method is best to use if you're configuring a small number of servers.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Hardware**.

2.  Click **Add Job Hardware**.

3.  Select **Linux / MacOS** or **Windows**. Install the Continuous Delivery agent by running the commands shown in the web UI on the machine you've designated as job hardware.

    **Important:** On Linux hosts, the agent installation process adds a sudoers rule to `/etc/sudoers.d/distelli` that enables the `distelli` user to run any command with sudo privileges and without requiring a password.

4.  When prompted for your email and password, enter the credentials associated with your Continuous Delivery for PE account.

    **Important:** Do not enter the root account credentials.

5.  At the prompt `To which hardware collection should this agent be added?` select the relevant workspace.

6.  In the Continuous Delivery for PE web UI, close the Add Job Hardware pane. You might need to refresh the Job Hardware page to see your newly configured job hardware.

7.  Click the **Job Hardware Active** toggle to mark your new job hardware as active.

8.  Click **+ Add Capability** and enter up to three capabilities associated with this piece of job hardware, such as PUPPET-AGENT or DOCKER, pressing **Save** after each addition.

    The Continuous Delivery agent automatically detects and displays four reserved capabilities: **WINDOWS**, **LINUX**, **DARWIN**, and **RASPBIAN**.

    When creating a job, you can specify which job hardware capabilities are required. This allows you to ensure that jobs requiring specific conditions, such as a particular operating system or piece of software, are run only on job hardware meeting those conditions.

    **Important:** In order to run jobs in Docker containers \(including [pre-built jobs](example_jobs.md#)\), make sure Docker is installed on your job hardware and set **DOCKER** as a capability.

    **Note:** Running Docker-enabled job hardware on an Alpine Linux base image requires the libgcc, bash, wget, and ca-certificates packages.


## Configure job hardware with `distelli.yml`

By setting up a `distelli.yml` file containing your user-specific agent credentials, you can configure job hardware without manually entering your account credentials.

### About this task

**Tip:** This method is best to use if you're configuring a large number of servers and don't want to enter your credentials manually each time.

### Procedure

1.  Create your agent credentials.

    1.  In the Continuous Delivery for PE web UI, click **Settings**.

    2.  Click **Hardware Agents**, then click **Create Agent Credentials**.

    3.  Continuous Delivery for PE creates an access token and secret key for your account. Click **Show** to display your secret key. Leave this page open while you set up your `distelli.yml` file.

2.  Create a file named `distelli.yml` and store it in a convenient location.

3.  Add the following to your `distelli.yml` file, pasting in the access token and secret key you generated in Step 1:

    ```
    DistelliAccessToken: <MY_ACCESS_TOKEN>
    DistelliSecretKey: <MY_SECRET_KEY>
    ```

    Save the file and exit.

4.  In the Continuous Delivery for PE web UI, click **Hardware**.

5.  Click **Add Job Hardware**.

6.  Select **Linux / MacOS** or **Windows**. SSH into the machine you've chosen to designate as job hardware. In sudo or Administrator mode, install the Continuous Delivery agent by running the commands shown in the web UI, adding `-conf <PATH_TO_DISTELLI.YML_FILE>` to the end of the `distelli agent install` command.

    For example, `/usr/local/bin/distelli agent install -conf <PATH_TO_DISTELLI.YML_FILE>`.

    **Important:** On Linux hosts, the agent installation process adds a sudoers rule to `/etc/sudoers.d/distelli` that enables the `distelli` user to run any command with sudo privileges and without requiring a password.

7.  At the prompt `To which hardware collection should this agent be added?` select the relevant workspace.

8.  In the Continuous Delivery for PE web UI, click **Hardware**.

9.  Click the **Job Hardware Active** toggle to mark your new job hardware as active.

10. Click **+ Add Capability** and enter up to three capabilities associated with this piece of job hardware, such as PUPPET-AGENT or DOCKER, clicking **Save** after each addition.

    The Continuous Delivery agent automatically detects and displays four reserved capabilities: **WINDOWS**, **LINUX**, **DARWIN**, and **RASPBIAN**.

    When creating a job, you can specify which job hardware capabilities are required. This allows you to ensure that jobs requiring specific conditions, such as a particular operating system or piece of software, are run only on job hardware meeting those conditions.

    **Important:** In order to run jobs in Docker containers \(including [pre-built jobs](example_jobs.md#)\), make sure Docker is installed on your job hardware and set **DOCKER** as a capability.

    **Note:** Running Docker-enabled job hardware on an Alpine Linux base image requires the libgcc, bash, wget, and ca-certificates packages.


## Configure global shared job hardware

Global shared job hardware can be used by all workspaces in your Continuous Delivery for PE installation. A super user must set up these special job hardware severs in the root console.

### About this task

A super user must perform this action. Once set up, all super users and the root user can view global shared job hardware in the root console.

### Procedure

1.  Log into the root console by selecting **Root Console** from the workspaces menu at the top of the Continuous Delivery for PE navigation bar.

2.  Click **Hardware**, then click **Add Job Hardware**.

3.  Select **Linux / MacOS** or **Windows**. Install the Continuous Delivery agent by running the commands shown in the web UI on the machine you've designated as global shared job hardware.

    **Important:** On Linux hosts, the agent installation process adds a sudoers rule to `/etc/sudoers.d/distelli` that enables the `distelli` user to run any command with sudo privileges and without requiring a password.

4.  When prompted for your login email and password, enter the credentials associated with your super user account.

5.  At the prompt `To which hardware collection should this agent be added?` select `Global shared hardware`.

6.  In the root console, close the Add Job Hardware pane. You might need to refresh the Job Hardware page to see your newly configured global shared job hardware.

7.  Click the **Job Hardware Active** toggle to mark your new job hardware as active.

8.  Click **+ Add Capability** and enter up to three capabilities associated with this piece of job hardware, such as PUPPET-AGENT or DOCKER, pressing **Save** after each addition.

    The Continuous Delivery agent automatically detects and displays four reserved capabilities: **WINDOWS**, **LINUX**, **DARWIN**, and **RASPBIAN**.

    When creating a job, users can specify which job hardware capabilities are required. This ensures that jobs requiring specific conditions, such as a particular operating system or piece of software, are run only on job hardware meeting those conditions.

    **Important:** In order to run jobs in Docker containers \(including [pre-built jobs](example_jobs.md#)\), make sure Docker is installed on your job hardware and set **DOCKER** as a capability.

    **Note:** Running Docker-enabled job hardware on an Alpine Linux base image requires the libgcc, bash, wget, and ca-certificates packages.


### Result:

Now that shared global job hardware is available, a **Use shared hardware** option is shown when creating or editing a job.

## Upgrade the Continuous Delivery agent

To upgrade the Continuous Delivery agent to a more recent version, reinstall the agent on top of the existing agent.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Hardware**.

2.  Click **Add Job Hardware**.

3.  Select **Linux / MacOS** or **Windows**. SSH into the machine you're using as job hardware. In sudo or Administrator mode, install the Continuous Delivery agent by running the commands shown in the web UI.

    **Important:** If you created a distelli.yml file to store your agent credentials, add `-conf <PATH_TO_DISTELLI.YML_FILE>` to the end of the `distelli agent install` command and skip to step 5 below.

4.  When prompted for your email and password, enter the credentials associated with your  Continuous Delivery for PE account.

    **Important:** Do not enter the root account credentials.

5.  In the Continuous Delivery for PE web UI, click **Hardware** and locate the newly upgraded server.

6.  Click the **Job Hardware Active** toggle to mark the upgraded job hardware as active.

7.  Click **+ Add Capability** and enter up to three capabilities associated with this piece of job hardware, such as PUPPET-AGENT or DOCKER, clicking **Save** after each addition.


