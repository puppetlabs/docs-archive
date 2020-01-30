---
layout: default
title: "Using containers"
---

From your project page, you can add, build, deploy, and automate your Docker containers. 

In Pipelines, the term "container" can refer to:
* The source code for a container in a software repo.
* A Docker container image in a container registry.
* A deployed (or running) container in a Kubernetes cluster.

If you'd like to know more about Docker and containers, see <a href="https://docs.docker.com/get-started/#containers-and-virtual-machines" target="_blank">Get started with Docker</a>.

You have two options when adding a container. If you have the source files for your container in a repo, select the option to **Connect Source Control**. Your source files must contain a `Dockerfile` that specifies how to build the container. You can use Pipelines to build, deploy and fully automate any container you've added from a repo. 

> **Remember**: The source files for your container should include a `Dockerfile`. Pipelines does not support Docker Compose. 

If you already have a pre-built image in a container registry, select **Specify Registry**. If you've added a container from a registry, you can't build the image in Pipelines, but you can deploy the container to Kubernetes, and automate it in a pipeline. 

After you've added a container to a project, it appears in the **Containers** section. From here, you can edit the source control and registry information for the container, change its build instructions, or delete the container. 

**Related topics**:
* For more information about container build instructions, see [Set build instructions](./container_build_instructions.md) .
* For more information about automating your containers in a pipeline, see [Set up a multi-stage pipeline](./pipeline-set-up.md) .
* For more information about deployments, see [Set up a deployment](./deployment-set-up.md) .
