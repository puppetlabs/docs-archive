---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configure job hardware

Job hardware, or the servers Continuous Delivery for Puppet Enterprise \(PE\) uses to run tests on your Puppet code, must be configured before you can begin running tests or using pipelines. 

There are two methods for authenticating your account when configuring job hardware. Which you choose to employ is mainly a question of how many servers you're designating as job hardware.

When installing the Continuous Delivery agent, you must enter the email address and password associated with your Continuous Delivery for PE account. Doing this manually is fine if you're only setting up a server or two, but if you're configuring a larger number of servers, adding these credentials can quickly become cumbersome. In this case, using a `distelli.yml` file to automatically pass your credentials to the agent is the more efficient method.

## Configure job hardware with the web UI

To designate a server as job hardware, install the Continuous Delivery agent on the server and mark it as active job hardware in the web UI.

### About this task

**Tip:** This method is best to use if you're only configuring a small number of servers.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Hardware**.

2.  Click **Add Job Hardware**.

3.  Install the Continuous Delivery agent by running the relevant **curl**, **wget**, or **windows** commands on the machine you've designated as job hardware.

    |Command type|Command|
    |------------|-------|
    |curl|`curl -sSL https://<WEB_UI_ENDPOINT>/download/client | sh`

`/usr/local/bin/distelli agent install`

|
    |Wget|`wget -qO- https://<WEB_UI_ENDPOINT>/download/client | sh`

`/usr/local/bin/distelli agent install`

|
    |Windows PowerShell|`powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://<WEB_UI_ENDPOINT>/download/client.ps1'))" & SET PATH=%PATH%;%ProgramFiles%/Distelli`

`distelli agent install`

|

4.  When prompted for your email and password, enter the credentials associated with your Continuous Delivery for PE account.

    **Important:** Do not enter the root account credentials.

5.  In the Continuous Delivery for PE web UI, close the Add Job Hardware pane. You might need to refresh the Job Hardware page to see your newly configured job hardware.

6.  Click the **Job Hardware Active** toggle to mark your new job hardware as active.

7.  Click **+ Add Capability** and enter up to three capabilities associated with this piece of job hardware, such as PUPPET-AGENT or PDK, pressing **Save** after each addition.

    **Important:** The Continuous Delivery agent automatically detects and displays four reserved capabilities: **Windows**, **Linux**, **Build**, and **Raspbian**.

    When creating a job, you can specify which job hardware capabilities are required. This allows you to ensure that jobs requiring specific conditions, such as a particular operating system or piece of software, are run only on job hardware meeting those conditions.


## Configure job hardware with `distelli.yml`

By setting up a `distelli.yml` file containing your user-specific agent credentials, you can configure job hardware without manually entering your account credentials.

### About this task

**Tip:** This method is best to use if you're configuring a large number of servers and don't want to enter your credentials manually each time.

### Procedure

1.  Create your agent credentials.

    1.  In the Continuous Delivery for PE web UI, click Settings ![](settings_icon.png).

    2.  Click **Agent**, then click **Create Agent Credentials**.

    3.  Continuous Delivery for PE creates an access token and secret key for your account. Click **Show** to display your secret key. Leave this page open while you set up your `distelli.yml` file.

2.  Create a file named `distelli.yml` and store it in a convenient location.

3.  Add the following to your `distelli.yml` file, pasting in the access token and secret key you generated in Step 1:

    ```
    DistelliAccessToken: <MY_ACCESS_TOKEN>
    DistelliSecretKey: <MY_SECRET_KEY>
    ```

    Save the file and exit.

4.  SSH into the machine you've chosen to designate as job hardware. Install the Continuous Delivery agent by running the relevant **curl**, **wget**, or **windows** commands in sudo or Administrator mode.

    |Command type|Command|
    |------------|-------|
    |curl|`curl -sSL https://<WEB_UI_ENDPOINT>/download/client | sh`

`/usr/local/bin/distelli agent install -conf <PATH_TO_DISTELLI.YML_FILE>`

|
    |Wget|`wget -qO- https://<WEB_UI_ENDPOINT>/download/client | sh`

`/usr/local/bin/distelli agent install -conf <PATH_TO_DISTELLI.YML_FILE>`

|
    |Windows PowerShell|`powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://<WEB_UI_ENDPOINT>/download/client.ps1'))" & SET PATH=%PATH%;%ProgramFiles%/Distelli`

`distelli agent install -conf <PATH_TO_DISTELLI.YML_FILE>`

|

5.  In the Continuous Delivery for PE web UI, click **Hardware**.

6.  Click the **Job Hardware Active** toggle to mark your new job hardware as active.

7.  Click **+ Add Capability** and enter up to three capabilities associated with this piece of job hardware, such as PUPPET-AGENT or PDK, clicking **Save** after each addition.  

    **Important:** The Continuous Delivery agent automatically detects and displays four reserved capabilities: **Windows**, **Linux**, **Build**, and **Raspbian**.

    When creating a job, you can specify which job hardware capabilities are required. This allows you to ensure that jobs requiring specific conditions, such as a particular operating system or piece of software, are run only on job hardware meeting those conditions.


