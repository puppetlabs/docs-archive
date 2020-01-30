---
layout: default
title: "Build hardware"
---

You can build images on shared Pipelines hardware, or use your own private hardware.

If you're using a Software as a Service (SaaS) version of Pipelines for Containers, you can use Pipelines shared hardware to build your containers. If you're using an on-premises version, or you'd prefer to build images on your own private hardware, you can use your own hardware. Alternatively, you can provision (or connect and existing) Google Cloud Project (GCP) instance, or an Amazon Web Services (AWS) Elastic Compute Cloud (EC2) instance through Pipelines.

Your private build hardware can still function as shared hardware within your organization, but it will operate differently to Pipelines shared hardware.  

## Using Pipelines shared hardware

> **Important**: The Pipelines build servers use the IP addresses housed in the <a href="https://github.com/puppetlabs/pipelines-ips" target="_blank">puppetlabs/pipelines-ips GitHub repository</a>. If your organization requires a whitelist of IP addresses, such as for a firewall, we recommend watching this repository for changes.
If whitelisting IP addresses isn’t an option, see our documentation on [setting up your own build hardware](./pfc_set_up_server.html).

When you build a container using Pipelines shared hardware, Pipelines uses a set of predefined Docker images to run the builds inside containers. Each image has different software installed to accommodate the builds of different languages. You can choose which image you want to use from the **Build Instructions** page on each container. 

The Docker image that you select is initiated as a running container. The build then takes place inside of this running container. This provides a pristine environment for each build. Pipelines terminates the container after the build is complete.

## How Pipelines for Containers queues builds on shared hardware

Builds are queued by application and branch. For example, if you have an application in Pipelines, tied to your repository and you're actively building from commits to the master branch, three consecutive commits to the master branch causes three build tasks to be scheduled. These are processed on a first-come, first-serve basis and do not run in parallel because they are for the same branch.

However, if you're actively building from commits to multiple different branches, and you make a number of simultaneous commits to the different branches, Pipelines builds all of the branches in parallel.

## Using private hardware

When you build an image using private hardware, the Pipelines agent executes each build in its own unique directory and then removes the directory after the build is complete. In other words, private builds do not follow the same "container within a container" process that Pipelines shared builds use.

## How Pipelines for Containers queues builds on private hardware

Builds on private hardware follow the same schema as those on the shared build servers. However, remember that private builds do not run inside Docker containers, which means parallel builds can affect each other.

Related Topics:
* For more information on the available shared Docker containers, see [Pipelines Build Environments](./build-environment.html). 
* For more information on adding your own build environments, see [Create a Docker build image](./docker-build-image.html). 
* For more information on the Pipelines manifest build steps, see [Pipelines for Applications: Pipelines Manifest](../for-apps/manifest-build.html).
* For more information on build instructions, see [Containers](./container.html).