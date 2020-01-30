---
layout: default
title: "Installing the Puppet Pipelines appliance"
---

The Puppet Pipelines appliance consists of two OVA files in OVF 1.0 format.

These images can be imported into most virtual hosting packages, including VMWare.

## System requirements

<ul>
  <li>The Puppet Pipelines build server must be able to communicate with Pipelines.</li>
  <li>The Puppet Pipelines build server must be able to communicate with Docker Hub to push/pull build images on setup.</li>
  <li>If the desired version of the enterprise software isn't already on disk, the Puppet Pipelines server requires internet connectivity to download software.</li>
</ul>

## Install and set up the appliance 

Use the links below to download the two images. 

1. [puppet-pipelines-server.ova](https://d36ys68ofvtij3.cloudfront.net/puppet-pipelines-server-2.1.01-RC3.ova)
    This is the main Puppet Pipelines server image. It includes MySQL databases configured for Pipelines and Artifactory, an install of Artifactory configured as a generic large object store, and Pipelines bits and install script on disk.
    
2. [puppet-pipelines-build-server.ova](https://d36ys68ofvtij3.cloudfront.net/puppet-pipelines-build-server.ova)
    This is a preconfigured Puppet Pipelines shared build server. This includes an installation of Docker and is pre-populated with various Docker build images. For more info on shared build servers, see [Set up Shared Build Images](./onpremises-setup.html#set-up-on-premises-docker-build-images).
    
### Install the Puppet Pipelines server 
    
1. Import the <b>puppet-pipelines-server.ova</b> image into your virtual hosting platform.

    > **Minimum requirements:** 6 GB RAM, two CPUs, 500 GB disk space.
    
1. Start the virtual machine. The default username for the VM is `pipelines` and the default password is `puppet`. 

1. SSH to the virtual machine as the `pipelines` user. 

1. Run `./pipelines-install.sh | tee install_output.log`. The script will bootstrap the Puppet Pipelines server. 

    This install script can be used as-is in simple environments. If needed, select variables can be overwritten to cusomize the installation:
    * `IPADDRESS` - defaults to the IP of the interface associated with the default gateway.
    * `PIPELINES_EMAIL` - username for the Pipelines web UI root user.
    * `PIPELINES_PASSWORD` - password for the Pipelines web UI root user.
    
1. Verify that Puppet Pipelines is up and running by navigating to the server at http://<IP_ADDRESS-or-FQDN>:8080. 
    
    If necessary, determine the IP or FQDN of the server by logging into the VM console as the `pipelines` user and running `facter ipaddress`. 
    
1. Install an [enterprise license](https://licenses.puppet.com), then log into the Puppet Pipelines web UI as the root user, using either the default credentials above or those you customized in the install script. 

### Install the shared build server

1. Import the <b>puppet-pipelines-build-server.ova</b> image into your virtual hosting platform. 

    > **Minimum requirements:** 4 GB RAM, one CPU, 200 GB disk space.

1. Start the virtual machine. Note that the IP address of the VM is shown as part of the VM console's login prompt.

1. On the command line, SSH to the virtual machine as the `pipelines` user. 

1. Run the setup script: 

   ~~~
   ./build-server-setup.py
   ~~~

    When prompted, enter the address of the Puppet Pipelines server.

1. Next, the setup script will install the Pipelines agent. When prompted, enter your agent credential access token and secret key, which can be found in the Pipelines web UI by clicking **Settings** > **Agent**.

    When the Pipelines agent starts, the shared Docker build images are rebuilt with the local agent embedded in them. 

At this point, when you open the Pipelines for Applications web UI, you'll see the new build server listed on the **Servers** page. 

### Update config file

Finally, update the Pipelines shared image Docker config file. This file can be found on the Pipelines server at: 

<code>/etc/distelli-config.json</code>

1. Locate `"DockerImages" : {` in the JSON blob, and update the file to include these entries:

   ~~~
   "gamma/*/DockerImages" : {
	   "pipelines-base": "Pipelines Base",
	   "pipelines-android": "Pipelines Android",
	   "pipelines-go": "Pipelines Go",
	   "pipelines-jvm": "Pipelines Java",
	   "pipelines-javascript": "Pipelines Javascript",
	   "pipelines-perl": "Pipelines Perl",
	   "pipelines-php": "Pipelines PHP",
	   "pipelines-python": "Pipelines Python",
	   "pipelines-ruby": "Pipelines Ruby"
   }
   ~~~

1. Adjust your file settings and save the file.

1. Restart the Pipelines agent supervise process by running: 

   ~~~
   sudo distelli supervise stop
   sudo distelli supervise start
   ~~~

Your Puppet Pipelines appliance installation is now complete and ready to be used.

<h3>Credentials</h3>

| Default credential for | Username | Password |
| --- | --- | --- |
| VM (SSH or console) | `root` | `puppet` | 
| VM (SSH or console) | `pipelines` | `puppet` | 
| MySQL (full privileges) | `root` | `puppet` | 
| MySQL (localhost access to `artifactory`) | `artifactory` | `puppet` |
| MySQL (localhost access to `pipelines`) | `pipelines` | `puppet` |
| Artifactory UI | `admin` | `password` |
| Pipelines web UI | `pipelines+admin@pipelines.vm` | `puppet` |

We strongly recommended changing these passwords. Realize that if you change a password, you also must change the password in the install script.


