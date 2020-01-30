---
layout: default
title: "Set build instructions"
--- 

After you've added a container to a project, you can set up build instructions for that container. 

Build instructions give you more control over what happens when Pipelines builds a container. The following options are available from the **Build Instructions** screen:
* **build**: The Docker commands that build and upload the container. Include each command on a new line. The default commands are: 
  
  ```
  ### Docker Build Commands ###
docker login -u "$DISTELLI_DOCKER_USERNAME" -p "$DISTELLI_DOCKER_PW" "$DISTELLI_DOCKER_ENDPOINT"
docker build --quiet=false -t "$DISTELLI_DOCKER_REPO" "$DISTELLI_DOCKER_PATH"
docker tag "$DISTELLI_DOCKER_REPO" "$DISTELLI_DOCKER_REPO:$DISTELLI_BUILDNUM"
docker push "$DISTELLI_DOCKER_REPO:$DISTELLI_BUILDNUM"
### End Docker Build Commands ###
  ```
   
* **afterBuildSuccess**: Any Docker commands you want to run if the build succeeds. Enter each command on a new line.
* **afterBuildFailure**: Any Docker commands you want to run if the build fails. Enter each command on a new line. 
* **Dockerfile Path**: The path to the container's `Dockerfile` in the source repo if it is not in the main directory.
* **Build Hardware**: Select **Build on shared hardware** to build inside a Docker container on Pipelines' shared hardware. Select **Build on my own hardware** if you've set up a private build server to use for container builds.
* **Build Images**: This option is available if you selected to build on shared hardware. The **Pipelines base** image is selected by default and suits most typical use cases. 
* **Build Capabilities**: This option is available if you selected to build on private hardware. Enter any build capabilities that you need and Pipelines will search the available build servers for matching capabilities.
* **Build Variables**: Any environment variables that you want to make available to the build environment. Enter these as `key=value` pairs. 

> **Important:** The Pipelines build servers use [the IP addresses housed in this GitHub repostory](https://github.com/puppetlabs/pipelines-ips). 
>
> If your organization requires a whitelist of IP adresses, such as for a firewall, we strongly recommend watching this repository for changes. 
>
> If whitelisting IP addresses isn't an option, see our documentation on [setting up your own build servers](./build-hardware.html#use-your-own-build-server). 

## Add a `latest` tag to a container

To add a `latest` tag to a container, add the following to the build instructions before `### End Docker Build Commands ###`:

```
docker tag "$DISTELLI_DOCKER_REPO:$DISTELLI_BUILDNUM" "$DISTELLI_DOCKER_REPO:latest"
docker push "$DISTELLI_DOCKER_REPO:latest"
```

Each time you build the container, Pipelines pushes it your container repository with a `:latest` tag.

Related topics:
* For more information about private build servers, see [Set up private build hardware
](./pfc_set_up_server).
* For more information about using shared hardware, see [Build hardware](./build-hardware.md).


