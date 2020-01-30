---
layout: default
title: "Sinatra application on Kubernetes"
--- 

In this tutorial I’ll show you how to use Pipelines to deploy a dockerized Ruby Sinatra application to a Kubernetes (K8S) cluster running on Google Container Engine (GKE). By the end of the tutorial you’ll be able to type an IP address into a web browser and have the Sinatra app render the following text: “Hello world from Distelli”.

## Prerequisites

You’ll need a Pipelines account. You can sign up for free.

In addition, make sure you have a Dockerhub account. You can obtain a DockerHub account by visiting https://hub.docker.com and signing up.

On DockerHub’s web interface, create a new repository for your account called “sinatra-app”.

## Build and deploy

Begin by configuring some required Integrations within the Pipelines web UI. Click the “Integrations” link on the top navigation menu. Here are the Integrations you’ll need to configure.

* DockerHub
* GitHub
* Google Cloud Platform

Next, make sure to fork the repository, sinatra-app, using your GitHub account so you’ll be able to connect it within the Pipelines web app. This repository contains the Ruby Sinatra application and Dockerfile—the latter which Pipelines uses to build the docker image. Once you’ve connected everything, please verify that you have a Project in Google Cloud Platform. If not, you’ll need to create a Google Cloud Platform project before proceeding.

Return to the Pipelines we app and create your first Pipelines Project. Name the project, sinatra-app, and give it a nice description. When you’re ready, click “Create Project”.

Next, click the “Add container” button and fill out the following form (see below). Make sure to select GitHub as your Source Control and sinatra-app as your repository (the one you forked earlier). For Registry information, choose DockerHub and fill in the Registry image with: <DockerHub username>/sinatra-app.

After creating your container, click “Build” in the upper right hand corner and follow the prompts to build the master branch of sinatra-app. When you’ve got everything selected, click “Build” and then click the link to view the build log. Make a note of the build number, we’ll need it again shortly in step 5.

After the build completes, you will want to provision a Kubernetes cluster on GCE. First, in Pipelines, click the “Clusters” link on the top navigation bar. Click the blue “Add Cluster” button and then click “New Google Container Engine Cluster” to start the creation flow.

Next, follow the steps in the UI to create and launch your cluster. There are helpful hints in each step of the flow to guide you along. If you run into any error messages while trying to create your cluster and are having trouble continuing the tutorial, feel free to reach out to us for help.

If you were successfully able to launch and provision your cluster on GCE, it’s time to deploy the Sinatra application to your cluster.

1. Back on your sinatra-app project, click the blue “Deploy” button.

1. Select your cluster from the list.

1. Click “Create a new Deployment”.

1. Paste the YAML deployment spec (see below) into the text box.

1. Change <your Pipelines username> to your Pipelines username and <Pipelines Build Number> to the build number that you recorded from step 3. (If you cannot recall, the build number is available on the Project timeline page).

1. When you’re ready, click the “Deploy” button and then click the “Monitor Deployment” button after the deployment begins. If there are any errors with the YAML Deployment spec, you’ll be prompted to correct them before proceeding. If the deployment is successful, your Kubernetes deployment spec will be saved in Pipelines. To deploy again, simply click “Modify an existing deployment” in the Deploy control room modal.

~~~
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
 name: sinatra-app
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
       deployment: sinatra-app
   spec:
     containers:
       — image: /sinatra-app:
       name: sinatra-app
~~~

Lastly, you will need to expose your deployment as a Service in order to make the app accessible from outside the Kubernetes virtual network. Since we’re using GCE, we’ll setup a Load Balancer Service using Pipelines.

1. Navigate to the Services section by clicking the blue “Services” button in the top right corner.

1. Select your cluster from the list.

1. Click “Create a new Service”.

1. Paste the YAML service configuration (see below) into the text box.

~~~
apiVersion: v1
kind: Service
metadata:
  name: sinatra-app
spec:
  ports:
    - port: 80
      targetPort: 4567
  selector:
    deployment: sinatra-app
  type: LoadBalancer
~~~

It might take a few minutes until the sinatra-app Load Balancer Service has an external IP address available.

1. After a few moments, click on the “Clusters” link on the top navigation bar and click on your cluster.

1. On the right side of the page, navigate to the Services section and find the service, sinatra-app.

1. If the Load Balancer service is ready, you’ll see the Load Balancer IP. If not, the page will update when an IP Address becomes available. I was given 104.196.235.166 but yours will be different.

1. Copy the IP address and paste it into your favorite web browser. You should see the text, “Hello World from Distelli”.

### Build and deploy automation

With Pipelines, you can setup the following type of automation:

* Action via source control: Commit, Pull Request, or Tag.
* Trigger: Pipelines build (with your choice of using either Pipelines hardware or your own hardware).
* Registry: push the built image to your connected Registry.
* Deploy: Automatically deploy your images to Kubernetes cluster pods.

Setting this up with Pipelines is easy. Here’s how:

1. On the sinatra-app project page, click the “Pipelines” tab.

1. On the right side, click the “Add Containers” button.

1. Add your container by checking the checkbox, and then select the appropriate branch.

1. Click “Add Container” and then close the Control Room modal.

1. By default, the container is setup to trigger builds when you make a commit to the specified branch of the source control repository. You can modify this behavior by clicking the “Auto Build” text on the container in the interface.

1. Next, click the “Add Stage” button. Select your cluster from the list. Check the default namespace. Type sinatra-app into the deployment name input box.

1. To auto deploy to your cluster on a successful build, check the “Auto Deploy on Build Success” button above your cluster in the Pipeline user interface.

To recap, you started with a Docker image of a Ruby Sinatra application. With Pipelines, you created a Project and configured a container for your Project. For this Pipelines Project’s first container, you connected the forked GitHub repository, sinatra-app, and configured the registry settings to utilize the DockerHub repository with the same name, sinatra-app. Then you built the container image using the Pipelines build system. Upon a successful build, Pipelines uploaded the image to your connected DockerHub repository and tagged it with the Pipelines build number.

Lastly, you were able to deploy the sinatra-app container image to your new Kubernetes cluster, expose a Load Balancer Service in GCE, and if you elected to do so, configure Kubernetes automation using the Pipelines Pipelines feature.




