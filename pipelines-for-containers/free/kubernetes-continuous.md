---
layout: default
title: "Continuous containers on Kubernetes"
--- 

Docker has won the container war and Kubernetes is now the leading cluster manager. In this tutorial I’ll walk you through continuously building and deploying a Docker container to a Kubernetes cluster running in Google Container Engine (GKE).

I’ll also walk you through setting up a pipeline so that any changes to your code get automatically built and deployed to your cluster.

## Step 1: Overview and requirements

Before we get started, lets gather up a few basic requirements. You’ll need the following:

* A Google Container Engine (GKE) account to run your cluster
* A GitHub account for your code
* A Docker Hub account to store your container image
* A Puppet Pipelines account to build and deploy

We’ll briefly touch on each of the Kubernetes pieces that we use and how they might fit into a microservice architecture that Kubernetes encourages, but for a deep dive check out kubernetes.io

We’ll be building and pushing a Docker container as part of this tutorial. If you’re already familiar with Docker and feel confident using Docker commands like `build` and `tag`, feel free to skip ahead to Step 2. 

## Step 2: Configure integrations

Once you’ve signed in to Pipelines click the “Integrations” link on the top navigation menu. Here are the integrations you’ll need to configure:

* DockerHub
* GitHub
* Google Cloud Platform

The example node app we’ll be building lives in the [NodeKubernetes GitHub repository](https://github.com/Distelli/NodeKubernetes). Fork it now using your GitHub account so you’ll be able to connect to it from within Pipelines. This repository contains your Node Express application and a Dockerfile, which Pipelines will use to build the Docker image.

Finally you’ll need a project in Google Cloud Platform that will host your kubernetes cluster.

## Step 3: Create a new project and build a container

Once your integrations are configured, start by creating a new Pipelines Project:

Click New Project at the top and name the project node-app. Give it a nice description. When you’re ready, click “Create Project”. This will create your Project and redirect your newly created Project overview page.

Next, click the “Add container” button and fill out the following form. Make sure to select GitHub as your Source Control and NodeKubernetes as your repository (the one you forked earlier). For Registry information, choose DockerHub and fill in the Registry image with: <DockerHub username>/node-app.

After creating your container, click “Build” in the upper right hand corner and follow the prompts to build the master branch of the NodeKubernetes repository. When you’ve got everything selected, click “Build” and then click the link to view the build log.

Once the build completes, Pipelines will create a docker image for your node application and push that image to your connected Docker Hub registry.

Navigate back to your node-app Project overview page and you should see several events corresponding to the steps we just took. 

Your event timeline may look slightly different from mine, but the important events are the the top two; a docker image, and a successful build. Make a note of the build number on the build event as you will need it later in this tutorial. My build number is 130038. You can view more details about these events by clicking on them.

## Step 4: Provision a Kubernetes cluster from Pipelines for Containers

Now that we’ve built a docker container, lets fire up a Kubernetes cluster in Google Container Engine (GKE)


Get started by clicking the “Clusters” link on the top navigation bar. You should have already connected GCE earlier when you set up integrations.


Click the blue “Add Cluster” button and then click “New Google Container Engine Cluster” to start the creation flow.


Follow the steps in the UI to create and launch your cluster. There are helpful hints in each step of the flow to guide you along. If you run into any error messages while trying to create the cluster and are having trouble continuing the tutorial, feel free to reach out to us for help.


Note: we are using GKE to host your Kubernetes cluste for the sake of simplicity. Pipelines can provision Kubernetes clusters anywhere, but GKE allows for a very simple Kubernetes cluster bootstrap with minimal manual configuration.


Once you have successfully launched your cluster, you will be redirected to the Cluster Overview page. The status of your cluster should be “provisioning.” Once it has finished provisioning, click on the cluster to view its details.


Your Cluster Details page should look something like this, with more details about your cluster at the top of the page.

Notice that by default there is already a single deployment, a single pod set, and a handful of services already running on your cluster. Here is a brief description of each:

 

The “kubernetes” service is used by Kubernetes to operate your cluster.

The “heapster” service, deployment, and pod set are used to run and operate Heapster, a utility to compute resource and usage analysis and monitor container clusters. Read more about Heapster here.

The “kube-dns” service is a deployment add-on used to facilitate DNS in Kubernetes. Read more about kube-dns here.

The “kubernetes-dashboard” service is used to expose the Kubernetes Dashboard UI. Read more about the Kubernetes Dashboard here.

 

For our purposes, we can ignore these components. However, it is worth noting that Kubernetes will automatically recreate these items if they are deleted.

## Step 5: Create a deployment specification

Now that we have a Docker image for our application and a running cluster, it’s time to create a deployment spec for our application and apply it to the cluster.


A deployment specification in Kubernetes is a method of specifying how your application container should be launched and run on Kubernetes. These specifications are highly configurable and allow you to fine tune almost anything you can think of. For our purposes, we will be using a very simple deployment spec that runs three pods each with a single copy of our container and the update pods via a Rolling Update.


Head back to your node-app Project overview page to get started. From here:

1. Click the blue “Deploy” button
1. Select your cluster from the list.
1. Click “Create a new Deployment”.
1. Paste the following YAML deployment spec into the text box: 

~~~
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 3
  strategy:
    rollingUpdate:
      maxSurge: 10%
      maxUnavailable: 10%
    type: RollingUpdate
  template:
    metadata:
      labels:
        deployment: node-app
    spec:
      containers:
      - image: your-DockerHub-username/node-app:build-number
        name: node-app
~~~

Next change your-DockerHub-username to your DockerHub username and build-number to the build number that you recorded from step 3. (If you cannot recall, the build number is available on the Project timeline page).

When you’re ready, click the “Deploy” button and the deployment will begin to your Kubernetes cluster. Click the “Monitor Deployment” button after the deployment begins to watch the progress. If there are any errors with the YAML deployment spec, you’ll be prompted to correct them before proceeding.

On the deployment monitor page, you can see details of the deployment that you just initiated, including the live status. Once this status reaches “success,” your deployment is complete. If there is an error applying your spec to your cluster, it will be shown on this page.

Click on the cluster name to view the details of your cluster. Notice that you now have a deployment named “node-app” and a corresponding pod set with three running pods, each with a copy of our node-app container.

Click “View Logs” on any of these three pods and toggle on the log. Once the connection is initiated, you should see the message “App Live on port 8080.”

Congratulations, you have successfully containerized and launched your application on a Kubernetes cluster! Read on and learn how to communicate with your container via Kubernetes services.

## Step 6: Expose a service and send traffic to your application

Kubernetes services define how sets of containers communicate with other sets of alike containers, or with the outside world. Containers running on a Kubernetes cluster cannot communicate with other containers without being exposed as a service. Any communication must be explicitly defined through a service specification. Read more about services here.

To demonstrate, we will set up a Load Balancer service to allow for outside access to our application.

Head back to your node-app Project overview page to get started. From this page:

1. Navigate to the Services section by clicking the blue “Services” button in the top right corner.
1. Select your cluster from the list.
1. Click “Create a new Service”.
1. Paste the following YAML service configuration into the text box.

~~~
apiVersion: v1
kind: Service
metadata:
  name: node-app
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    deployment: node-app
  type: LoadBalancer
~~~

Click “Create Service” to create the new service.


After a few minutes, Kubernetes will have allocated an IP for your service and assigned it to the port specified in our specification. We specified port 80 so we can access this service over HTTP using a web browser. Kubernetes then maps port 80 to targetPort 8080 on our node-app container where our application is being served.

Since the type of this service is a LoadBalancer, Kubernetes will automatically balance the requests across all three running instances of our node-app. This makes it easy to scale depending on traffic. If traffic increases, simply change the number of replicas in your deployment spec and redeploy and your load balancing service will take care of making sure that the each container receives about the same amount of traffic.

Now that we have our service created, click Clusters on the top navigation bar and click on your cluster to view its details.

To obtain the IP that Kubernetes assigned to your service:

On the right side of the page, navigate to the Services section and find the service node-app. Click the “Service Details” button for this service.

If the Load Balancer service is ready, you’ll see a JSON object with a loadBalancer property, which contains the IP address. The IP address I was given is 130.211.183.161. Yours might be different.

Paste this IP into a new tab of your browser. If everything is working property, you should a nice little hello world message like this:

## Step 7: Configure a pipeline for continuous delivery

We now have a containerized application running on Kubernetes that is accessible from the outside world. I’ll now show you how to update the running containers every time you commit code to your GitHub repository. Enter Pipelines.

We realized that the process of updating a Kubernetes cluster with the latest code changes was more work than it needed to be. We envisioned a seamless process where new changes would be built into a docker container, uploaded to a registry, and automatically deployed to your cluster using a pre-existing deployment spec.

This allows for a speedier development process where code changes can be tested on a staging cluster almost immediately after being pushed with very minimal user interaction. Changes that work well on staging can be promoted to a production cluster with one click.

A Pipeline automates the following tasks in order:

1. Trigger — Pipelines receives a webhook from your source repository when changes have been made. This is the beginning of your Pipeline, referred to as a trigger. You can specify when to trigger the Pipeline from the options of Commit, Pull Request, and Tag.

1. Build — Once your Pipeline has been triggered, Pipelines will clone your repository on the branch you specified when you added the container to your pipeline and build your repository into a Docker image using the Dockerfile residing in your repo.

1. Push — Now that your source has been built into an image, Pipelines will push the resulting image to the registry you specified when you added the container to your project. Your image will be tagged with the build number of the build that the image resulted from. With this, you get a copy of the image stored outside of Pipelines that you can use anywhere.

1. Deploy — Now that an image has been built, Pipelines can auto deploy it to one or more clusters with zero human interaction.

 Lets take a deeper dive into the components that make up a Pipeline and setup a basic pipeline for our project.
 
## Step 8: Container pipelines to one or more Kubernetes clusters

To configure a Pipeline, navigate back to your node-app Project overview page and click “Pipelines” from the sub navigation menu.

1. Click the blue “Create a Pipeline” button on the right side of page.

1. Enter a name for your Pipeline. Mine is called node-app-pipeline.

1. Click the blue “Add Containers” button in the middle of the screen. This will bring up a menu where you can select containers to add to your pipeline.

1. Select “node-app.”

1. Click on the input box and select the master branch.

1. Click “Add Containers.”

Let’s break down this process:

In the add containers menu, the containers in your project are listed. Note that in order to add a container to your pipeline, it must first be a part of your project. When you select a container, you must also select a branch. When changes are made to this branch, Pipelines knows to build the branch into a Docker image and push the resulting image to the registry that you specified when you first added the container to your project.

The ability to select which branch your image is built from allows for easy separation between development branches and production branches. There are many ways to set up a Pipeline depending on your individual development process, and we’ve tried to make them flexible enough to accommodate many common use cases.

Now that we have a container, we need to set the build triggers. On the node-app container in your Pipeline, click on “Auto Build” and make sure that the “Commit” is selected. Then click the red close button.

Now anytime your repository is pushed to, your Pipeline will be triggered.

Once you have added the node-app container to your Pipeline and have the build triggers configured, you will need to create a stage.


A stage is a group of one or more clusters that will be deployed to at the same time. Note that a single stage can contains multiple clusters and a single Pipeline can contain multiple stages. For example, at Pipelines we have we three stages: Beta, which is our dev environment, Gamma, which is another dev environment with a slightly different configuration, and Production. Each of these stages contains a single cluster.


When the Pipeline is triggered and an image is successfully created, our Pipeline is configured to automatically deploy to the Beta environment. If this deployment is successful, the Gamma environment is then deployed to. This gives us several environments to test our changes in, and once we have verified that the changes are working properly in each dev environment, we can easily promote them to our Production environment.

To set up a Pipeline stage with a single cluster:

1. Click the blue “Add Stage” button on the container pipeline page in Pipelines.
1. Select the cluster you created in step 4
1. Select the “default” namespace. For more information on Kubernetes namespaces, see the Kubernetes documentation for services
1. Enter “node-app” in the “Deployment Name” field. It is important that this field is exactly the same as the “name” field in the deployment spec that we specified in part 5. We will be using the deployment spec created in part 5 to deploy our container to our cluster.
1. Click the blue “Add Stage” button
1. Click “Close”

Notice how your view has changed. A stage has been added to your Pipeline, and you have several configuration options available.

The most notable of these is the “Auto Deploy on Build Success” option. Make sure this is checked.

Our Pipeline is now configured and is ready for use. Head to the NodeKubernetes Github repo that you forked earlier and clone it to your local machine.

Change the server.js file to look like this:

~~~
const express = require('express')
const app = express()
const PORT = process.env.PORT || 8080;
let userRouter = express.Router()
userRouter.get('*', (req, res) => {
 res.status(200).json({hello: 'world of automated devops tasks!'})
});
app.use(userRouter)
app.listen(PORT, () => {
 console.log('App Live on port ' + PORT)
});
~~~

Commit and push these changes to your repository and head back to Pipelines to watch the magic happen. Click on the “Containers” tab and notice the two new events that have been created, one for the push that we did to our repository with code changes, and one for the resulting build.

On the Pipelines tab, you will see information about the build that is currently running. Once the build in complete, the image created during the build will be deployed to our cluster using the deployment spec that we created in part 5.

Pretty nifty if I do say so myself.

When the status of the deployment reaches “success,” you deployment is complete. You Pipeline view should look something like this:

Navigate back to the IP address that Kubernetes allocated for your service. It will be the same as in part 6. You should see the updated message:

## Conclusion: Automation is king

In today’s world of high performance clusters, quick development cycles, and a seemingly endless number of tools, it’s become increasingly necessary to automate complex tasks, often by combining those tools that are best fit for the task at hand. Not only does automation cut back on development time and save you from the headaches of repetition, it also ensures results are consistent and minimizes the chances of human error. I hope you will join us on our journey to conquer the cloud.

