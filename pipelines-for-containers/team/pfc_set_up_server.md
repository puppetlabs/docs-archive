---
layout: default
title: "Set up private build hardware"
---

Instead of using Pipelines shared hardware, you can build your container images on a private server.  

Private build servers are a requirement for on-premises installations of Pipelines for Containers. They are also useful for the following reasons:
* A private build server allows your builds to access resources behind your corporate firewall.
* Large, complex builds perform more predictably on dedicated hardware. 
* Your security policy may prohibit you from building software on the same hardware as other users.    

Pipelines uses an agent model to provide server communication. For information, see [The Pipelines agent](./agent.html).

To get started, install and set up the Pipelines agent on your server. The agent manages image builds and provides you with a command-line interface for Pipelines. After you've set up your agent, mark your server as a build server in Pipelines. Finally, edit the build instructions for your containers to ensure that they build on your server.    

Before you get started:
* <a href="https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository" target="_blank">Install Docker</a> on your server.
* <a href="https://help.github.com/articles/set-up-git/" target="_blank">Install Git</a> on your server.

To set up your private build server:
1. Set up the agent. If you created an instance through Pipelines, the agent should already be installed. You can skip ahead to step 2, "Mark your server as a build server in Pipelines."    
   > **Important**: Installing the Pipelines agent requires administrator (root) privileges. 
   
   1. Download the agent using curl or wget:

      ```sudo curl -sSL https://pipelines.puppet.com/download/client | sh```


      ```wget -qO- https://pipelines.puppet.com/download/client | sh```
      
    1. Install the agent:

       ```sudo /usr/local/bin/distelli agent install```

    1. When prompted, use your Pipelines credentials to log in. The agent begins automatically. 
    1. (Optional) Check to see if the agent is running:
       
       ```sudo distelli agent status```
       
    1. Make `distelli` a member of the `docker` group. This gives Pipelines the correct permissions to run Docker builds on your server. 

       ```sudo usermod -aG docker distelli```

    1. Restart the agent to make sure that your changes take effect.
    
       ```sudo distelli agent restart```

1. Mark your server as a build server in Pipelines.
   1. Log in to your Pipelines account. 
   1. Click **Build Hardware**.
   1. Select your server.
   1. Select **Build Server**. Your server is tagged as a build server on the **Build Hardware** page.
1. Edit the build instructions for any containers that you want to build on your server. 
   1. From your **Project** page, select a container and click **Build Instructions**.
   1. Select **Build on my own hardware**.
   1. (Optional) If you're using Docker version 17.06+, remove `-e "$DISTELLI_DOCKER_EMAIL" "$DISTELLI_DOCKER_ENDPOINT"` from the build instructions. The `--email (-e)` flag is not supported in newer versions of Docker.
1. Build your containers from your **Project** page. 

You've set up your containers to build from a private server.

Related topics: [Build hardware](./build-gardware.html), [The Pipelines agent](./agent.html), [Automated agent installation](./agent-automate.html)

