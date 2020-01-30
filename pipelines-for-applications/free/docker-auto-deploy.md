---
layout: default
title: "Auto-deploy Docker containers"
---

When integrating Pipelines for Applications deployments with other third-party CI systems, there may be a desire to build Docker images with this CI system, but have Pipelines for Applications facilitate auto-deploy of these images as running Docker containers. This is possible with a few steps.

## Create an application

First, an application will need to be created in Pipelins for Applications. This application "can be" or "not be" connected to a software repository, but it MUST be connected to a Docker registry. Please follow the appropriate instructions here.

* [Creating an application without a repository](./application-create.html)
* [Enabling Docker for a new application](./docker.html)

## Prepare distelli-manifest.yml

Next a `distelli-manifest.yml` file must be created. This file will contain a minimum [PkgInclude](./manifest.html) section, and the instructions to deploy your container. Here is an example.

~~~
jdoe/myapp1:
  PkgInclude:
    - <b>distelli_jenkins_env.sh</b>
  Exec:
    - <b>source distelli_jenkins_env.sh</b>
    - cid=$(uuidgen)
    - trap 'sudo docker stop $cid' SIGTERM
    - echo "sudo -E docker run --name=$cid $DISTELLI_DOCKER_ENVS --rm=true $DISTELLI_DOCKER_PORTS  $DISTELLI_DOCKER_REPO:$DISTELLI_BUILDNUM"
    - (sudo -E docker run --name=$cid $DISTELLI_DOCKER_ENVS --rm=true $DISTELLI_DOCKER_PORTS  "$DISTELLI_DOCKER_REPO:$DISTELLI_BUILDNUM") &
    - wait
    - "true"
~~~

This file must be created and available during the Docker build process. Typically this file is checked into the root of the software repository that builds the Docker container.

## Build the image and do the <b>distelli push</b>

Finally, the steps that are either taken from the command line, or with a third-party CI system, consist of the following. (Imagine this as part of a Jenkins build shell script.)

~~~
docker login
docker build -t DOCKERREPO/DOCKERIMAGE
docker tag DOCKERREPO/DOCKERIMAGE DOCKERREPO/DOCKERIMAGE:DOCKERTAG
docker push DOCKERREPO/DOCKERIMAGE:DOCKERTAG
echo "export DISTELLI_DOCKER_REPO=$DOCKERREPO/$DOCKERIMAGE" > distelli_jenkins_envs.sh
echo "export DISTELLI_BUILDNUM=$DOCKERTAG" >> distelli_jenkins_envs.sh
distelli push
~~~

With the above, the following values will have to be specified or manually supplied:
<ul>
  <li><b>DOCKERREPO</b> - The Docker repository identifier. If you're using a free DockerHub repository, this would be your DockerHub username.</li>
  <li><b>DOCKERIMAGE</b> - The Docker repository image name.</li>
  <li><b>DOCKERTAG</b> - The unique tag identifier. If not using unique tags, use "latest".</li>
</ul>

When doing the <code>distelli push</code>, the Pipelines CLI will consult the `distelli-manifest.yml` file to create an application release, which will include the deploy instructions from the `distelli-manifest.yml` file and the `distelli_jenkins_env.sh` file created earlier.

> **Note:** The above assumes the Distelli CLI is installed on the system that is building the docker image and pushing to Distelli. For more information see: [Installing the Distelli CLI](./cli.html).
