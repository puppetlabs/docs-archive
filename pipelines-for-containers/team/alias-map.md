---
layout: default
title: "Map a container image to multiple deployment containers"
---

Map multiple containers to a single container image and automate them in a pipeline.

Mapping multiple containers to a single container image is useful in situations where you've packaged all of your services into a single container. For example, if your image includes a web server, a proxy, and application content, you can run that image in three containers and configure each container for a separate service. 

All of your containers use the same image and build tag, for example, `my-repo/example-app:111111`. Pipelines dynamically updates the tag with a Pipelines Globally Unique Identifier (GUID) during builds and kubernetes deployments. To ensure that Pipelines accurately updates the build tag for each container instance on subsequent builds and deployments, you must use a pipeline to map the containers in the deployment to the container image in your project (henceforth referred to as a project container).

Before you begin:
* Make sure you're familiar with the workflows and requirements for [setting up a deployment](./deployment-set-up.md) and [creating a pipeline](./pipeline-set-up.md). 
* Create an image for your application using a Dockerfile, add the container to a Pipelines project, and build the container.
> **Note**: You must add the container to Pipelines using the **Connect Source Control** option. For more information, see [Using containers](./container.md).
* Identify the unique settings you need for each container and create a YAML deployment spec for your containers. These typically include ports, arguments, and commands.

A typical YAML deployment specification looks like this:

```
   containers:
        - name: website
          image: example/example-app:111111
          command: ["start_apache.sh"]
          args: ["HOSTNAME", "KUBERNETES_PORT"]
        - name: proxy
          image: example/example-app:111111
          ports:
          - containerPort: 8000
            hostPort: 80
            protocol: TCP
            name: nginx
          command: ["start_nginx.sh"]
          args: ["USERKEY"]
        - name: content
          image: example/example-app:111111
          command: ["start_content.sh"]
          args: ["CONTENT_DIR"]

```

To map your project container to multiple deployment containers:
1. Create a deployment:
   1. From your project, click **Deploy**.
   1. Click **Select Cluster**, and select your cluster.
   1. Click **Select Namespace** and select a namespace.
   1. Enter a deployment name and a helpful description.
1. Add the project container to the deployment:
   1. Click **Select Container** and select a container to add.
   1. Click **Select Image Tag** and select a build. The most recent build is always at the top of the list.
   1. Click **Add Container**. The container you just added is listed under the Containers tab.
   1. (Optional) Click the container and configure its port mappings, volume mounts, and environment variables. If you have a YAML spec for the container, skip to the next step.
1. Add an instance of the container for each service you need:
   1. Click **YAML**.
   1. Under **Containers**, enter or paste in your YAML specifications for the containers you need. The `image` values must be the same for all of the containers. For example:
      
      ```
      containers:
        - name: website
          image: example/example-app:111111
          command: ["start_apache.sh"]
          args: ["HOSTNAME", "KUBERNETES_PORT"]
        - name: proxy
          image: example/example-app:111111
          ports:
          - containerPort: 8000
            hostPort: 80
            protocol: TCP
            name: nginx
          command: ["start_nginx.sh"]
          args: ["USERKEY"]
        - name: content
          image: example/example-app:111111
          command: ["start_content.sh"]
          args: ["CONTENT_DIR"]```
      
      > **Tip**: Give each instance of the container a unique `name`.
   
1. Click Deploy and allow some time for the containers to deploy and connect.
   1. Map the deployment containers to the project container in a pipeline:
      1. Create a pipeline:
      1. Click **Pipelines**.
      1. Click **Create Pipeline** and enter a descriptive name. For example, _WordPress Pipeline_.
      1. Click **Add Containers** and select the project container and branch.
      1. Click **Save Source**.
      1. Click **Close**.
   1. Add a deployment stage:
      1. Click **Add Deployment Stage** and use the following settings:
         * **Cluster**: Select your cluster.
         * **Namespace**: Select the namespace where you deployed the container.
         * **Deployment**: Select the deployment.
         * **Container mappings**: For each container listed under Deployment Containers, click **Do not map to project container** and select the project container. For example, _example-app_. This maps the deployed containers to your project containers and ensures that Pipelines accurately updates the build tag for each container instance on subsequent builds and deployments.	
      1. Click **Close**.
   1. Use auto-deploy to automate your pipeline:
      1. Under **Source**, click **Auto Build** and then select one or more auto-build conditions to apply to the project container.
      1. Click **X**.
      1. Ensure that **Auto Promote on Image Event** is selected.

You've mapped your project container to multiple deployment containers. On each successful build, the stage in your pipeline automatically updates the deployment containers with the new version of your project container.

Related topics:
* For more information about deployments, see [setting up a deployment](./deployment-set-up.md)
* For more information about pipelines, see [creating a pipeline](./pipeline-set-up.md)
* For more information about containers, see [Using containers](./container.md).
       		
