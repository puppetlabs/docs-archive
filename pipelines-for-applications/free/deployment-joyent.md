---
layout: default
title: "Joyent application: Deploy"
---

Use Pipelines to call any CLI or API during builds or deploys.

Before you begin the tutorial you must:

* Install the Pipelines CLI on the development system. [Installing the CLI](./cli.html)
* Ensure git is installed on the development system. [Download Git](http://git-scm.com/downloads)
* Have a destination server available to deploy software to. 
* Install the Pipelines agent on the destination server. [Installing the agent](./agent.html)

This tutorial is an exercise in implementing the Joyent triton implementation in this repository <a href="https://github.com/autopilotpattern/wordpress">https://github.com/autopilotpattern/wordpress</a> with Pipelines.

"This project uses the Autopilot Pattern to automate operations, including discovery and configuration, for scaling to any size. All component containers use ContainerPilot and Consul to configure themselves. This also allows each service to be scaled independently to handle incoming traffic and as more services are added, the containers that consume these services will reconfigure themselves accordingly."

That is a quote from the repository Readme. There are a several advanced options described in the repository, that this tutorial does not cover, but can easily be extended to those advanced configurations.

Ultimately, this will create a Wordpress service accessible to the public.

<h2><a name="create-a-joyent-account"></a>Create a Joyent Account</h2>

To implement this tutorial, you must have an account in Joyent. You can create one <a href="https://my.joyent.com/landing/signup/">here</a>. You must have a paid account for the features in this tutorial.


<h2><a name="create-manta-bucket"></a>Create Manta Bucket</h2>

You must first setup a Manta "directory" or "bucket" to store information. I recommend creating this "bucket" in the <b>stor</b> directory. See the <a href="https://apidocs.joyent.com/manta/">Manta Docs</a> for more information.


<h2><a name="create-a-build-server"></a>Setup a Build Server</h2>

To complete this tutorial, you must next setup a Pipelines build server. You can follow the steps <a href="./server.html">here</a>.


<h2><a name="install-prerequisites"></a>Install Prerequisites</h2>

On the build server you will have to install prerequisites. These should distinctly be installed under the <b>distelli</b> user account. One way to ensure the prerequisites are installed under the distelli account is to <b>switch user</b> to the distelli account like this:

~~~
sudo su - distelli
bash
~~~


<h3><a name="install-docker-and-docker-compose"></a>Install Docker and Docker-Compose</h3>

As the distelli user, on the build server, you must install Docker. Instructions for installing Docker can be found <a href="https://lb.docs-docker-com.99f58319.svc.dockerapp.io/engine/installation/">here</a>.

The <b><i>distelli</i></b> user should have full access to the docker daemon. This can be accomplished by adding the distelli user to the docker group. Note, you must restart the distelli supervise process after adding it to the docker group.

~~~
sudo distelli supervise stop
sudo distelli supervise start
~~~


<h3><a name="install-nodejs-and-npm"></a>Install NodeJS and NPM</h3>

This example uses Joyent triton. To install triton, you must first install nodejs. The following exemplifies installing nodeJS on Ubuntu 16.04.

~~~
sudo apt install -y nodejs npm
sudo ln -s /uar/bin/nodejs /usr/bin/node
~~~


<h3><a name="install-triton-smartdc-and-json"></a>Install Triton, Smartdc, and Json</h3>

~~~
sudo npm install -g triton
sudo npm install -g smartdc
sudo npm install -g json
~~~

<h3><a name="create-ssh-key-and-add-to-joyent"></a>Create SSH key and add to Joyent</h3>

Next you will have to create an SSH key, on the build server, and add it to Joyent. Use the <code>ssh-keygen</code> command. You can add your ssh key to Joyent <a href="https://my.joyent.com/main/#!/account">here</a>.

Remember, this must be done for the <b>distelli</b> user.

When adding the key to Joyent, ensure the key has access to Manta.


<h2><a name="configure-triton-profile"></a>Configure Triton Profile</h2>

You will have to create a triton profile on the build server. You can begin this process with <code>triton profile create</code>. I would recommend the following values:

<ul>
  <li><b>name</b> wordpress</li>
  <li><b>account</b> YOUR_JOYENT_ACCOUNT</li>
  <li><b>fingerprint</b> SSH_KEY_FINGERPRINT</li>
</ul>

Remember, this must be done as the <b>distelli</b> user.

Note, SSH_KEY_FINGERPRINT is typically <code>~/.ssh/id_rsa.pub</code>


<h2><a name="validate-triton"></a>Validate Triton</h2>

You can validate your install by creating an instance in Joyent with the CLI. Here is an example CLI command.

~~~
triton instance create -w --name=distelliexample ubuntu-certified-14.04 k4-general-kvm-3.75G
~~~

Don't forget to terminate this to ensure you are not billed.

You may need to run the following first.

~~~
eval $(triton env --docker)
~~~

<h2><a name="setup-cns"></a>Setup CNS</h2>

This exercise requires that you have enabled <b>Container Name Server</b> (CNS) in Joyent. Note, this may also be referred to as <b><i>consul</i></b> server in this document and the repository referenced above.

By default, this tutorial distinctly expects the CNS server to be named "consul". Here is an example CLI command to create the server. Note, you must first <a href="https://docs.joyent.com/public-cloud/network/cns/usage#using-triton-cns-with-triton-cli">enable CNS</a> on your Joyent account.

~~~
triton instance create -w --name=consul -t triton.cns.services=consul ubuntu-certified-14.04 k4-general-kvm-3.75G
~~~

<h2><a name="fork-the-repository"></a>Fork the Repository</h2>

You must create a fork of the example repository</a> <a href="https://github.com/autopilotpattern/wordpress">https://github.com/autopilotpattern/wordpress</a>


<h2><a name="setup-distelli-application"></a>Setup Pipelines Application</h2>

Now we are ready to create an application in Pipelines for the forked repository. To create an application, please see <a href="./application-create.html">Creating an Application</a>.

This application will be connected to the repository you forked above.

You will need to add the following [build environment variables](./build-configure.html):

<ul>
  <li><b>MANTA_BUCKET</b> - The folder/bucket name in Joyent Manta</li>
  <li><b>MANTA_USER</b> - The user that has access (via ssh key) to the Manta folder/bucket</li>
</ul>

<h2><a name="setup-manifest"></a>distelli-manifest.yml</h2>

The final setup is to add the appropriate steps to the Pipelines manifest.

You will add these steps in the AfterBuildSuccess section of the manifest. On a successful build, the steps will initiate the wordpress cluster in Joyent.

~~~
  AfterBuildSuccess:
    - eval $(triton env)
    - ./setup.sh ~/.ssh/id_rsa.pub
    - sed -i "s|MANTA_BUCKET= # an existing Manta bucket|MANTA_BUCKET=$MANTA_BUCKET|g" _env
    - sed -i "s|MANTA_USER= # a user with access to that bucket|MANTA_USER=$MANTA_USER|g" _env
    - docker-compose up -d
    - echo "The Wordpress URL is:"
    - grep "WORDPRESS_URL=htt" _env
~~~

To break this down, here is what is occurring.

<ul>
  <li><b>eval $(triton env)</b> - Setup the Joyent triton environment.</li>
  <li><b>./setup.sh ~/.ssh/id_rsa.pub</b> - Create the _env file</li>
  <li><b>sed -i "s|MANTA_BUCKET= # an existing Manta bucket|MANTA_BUCKET=$MANTA_BUCKET|g" _env</b> - Update the _env filw with the appropriate values</li>
  <li><b>sed -i "s|MANTA_USER= # a user with access to that bucket|MANTA_USER=$MANTA_USER|g" _env</b> - Update the _env file with the appropriate values</li>
  <li><b>docker-compose up -d</b> - Bring the service up</li>
  <li><b>cat _env | grep URL</b> - Find the triton CNS (DNS) name for the service</li>
</ul>

Once you have those steps configured, build your application! Note, the services take 10-20 minutes to come up.