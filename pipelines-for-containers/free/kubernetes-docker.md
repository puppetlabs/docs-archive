---
layout: default
title: "Docker Compose to Kubernetes"
--- 

Docker has become the container scheduler of choice to run containerized applications in a way that makes them portable and reproducible. Many developers have incorporated Docker into their workflows and have created containerized applications that work in concert with one another. These applications, also known as services, are often run together using Docker Compose.

With the rise of Kubernetes as the management platform of choice for containerized applications, many developers are now seeking to take their Docker Composed applications and deploy them to a Kubernetes cluster. This tutorial will show you how to take a simple application composed of two services that are run together using Docker Compose and automate its build, test and deployment processes to your Kubernetes cluster.

By the end of this tutorial you should be able to push to a GitHub repository to trigger a build of two separate services. The build of each service will create a Docker image which will be uploaded to a Docker image registry. After a successful upload, these images will be automatically applied to a Kubernetes Pod via a new Kubernetes deployment.

## Step 0: Prerequisites
 
* A Google Cloud Platform Account to run your Kubernetes Cluster and store your Docker images
* A GitHub account for your application source code
* A Puppet Pipelines Account to facilitate the automation of building, testing and deploying your Docker Compose application

## Step 1: Fork the application repository
 
Fork the repository [distelli/golang_docker_kubernetes](https://github.com/Distelli/golang_docker_kubernetes). This repository encapsulates a simple golang application that is composed of two services. The first service, `api_service`, is a trivial service that listens on port 8001 and responds to GET requests at endpoint /api/greeting.

The second service, `client_service`, is a trivial service that listens for user requests on port 8080, communicates with the api_service, and responds to requests with a greeting to the user. Each of these services is containerized using a separate Docker file, and the applications are run together using Docker Compose.

Here is the Docker Compose file for the repository:

~~~
version: '2'
services:
  client_service:
    build: ./client_service
    ports:
      - "8080:8080"
    links:
      - api_service
    environment:
      - api
  api_service:
    build: ./api_service
    ports:
      - "8001:8001"​
~~~

You can run the application locally by specifying the following commands from the root of the repository:

~~~
$ export API_SERVICE="http://$(docker-machine ip):8001" 
$ api=$API_SERVICE Docker Compose up
~~~

To visit the running application from the browser, first get the IP address of the running applications by running `docker-machine ip`. (See [this tutorial on docker-machine](https://distelli.engineering/mac-os-x-build-a-docker-image-run-an-image-as-a-container-locally-using-docker-machine-and-push-d78112c5c5b5) if you need more background.)

 
Then, in your browser, navigate to `<IP_ADDRESS>:8080`. You should see a "Hello world" message. 
 
Now try adding your name in the query string, for example <IP_ADDRESS>:8080?name=<YOUR_NAME>. Your "Hello world" message is now personalized.

This, of course, is a very trivial application, but it is constructed to mimic more complex applications that are built using a service-oriented architecture and communicate with one another to arrive at a value that is desired by an end consumer.

## Step 2: Building and testing your services

Before we launch our application, we want to make sure that both services are built and composed together. However, since each service is containerized separately using a unique Dockerfile, we want to store the Docker images for each service in separate Docker image repositories. Doing so allows us to use the images for each service independently, and also prepares us to deploy it to a Kubernetes cluster where each image must be encapsulated independently.

1. Log in to your Puppet Pipelines account and set up integrations with a source control and cloud provider.  

1. In the Pipelines for Containers web UI, navigate to the **Projects** tab and click **New Project**. Give your project a name and a description and click **Create Project**.

1. Add the two services from the golang_docker_kubernetes application as two separate containers by clicking **Add Container**. Select **Connect Source Control**, as we will be building our containers with Puppet Pipelines.

    Create two containers, named "api-service" and "client-service," and connect each to your source control and Docker registry provider. 
    
    > **Note:** Navigate to your Google Cloud Account to find the name of your Google Cloud Project, which must be specified in the image field if using Google Cloud.

    After you have successfully added the two containers for client-service and api-service, it is time to set the build instructions and trigger a build.

1. Set build instructions for each container. Return to the **Projects** screen and select your project. Click the tools (**Build Instructions**) icon in the container panel. 

    Puppet Pipelines injects instructions to build, tag, and upload an appropriate image by default. Under build instructions you can modify these commands and add additional commands to facilitate tests. We are going to use these commands unmodified.

    For both "api-service" and "client-service," change the Dockerfile location so that the Pipelines agent can find the correct Dockerfile to use during the build. The Dockerfile for "client-service" should be at `./client_service`, while the Dockerfile for "api-service" should be at `./api_service`.

1. When you are done updating the build instructions, trigger a build for each container by clicking the hammer (**Build**) icon at the top of the screen on the project page.

    When these builds complete you should see events indicating the completion of these builds. Note that Pipelines for Containers stores rich information about each image it builds, you can see this information by clicking on either of the built images.​

## Step 3: Provision a Kubernetes cluster (or sync an existing one)

Kubernetes automates the deployment, scaling and operations of containerized applications across clusters of hosts. With Puppet Pipelines, you can provision a new Kubernetes cluster on any cloud in just a matter of minutes.

 1. Click the **Clusters** tab, and then create or sync your cluster. 
 
    * If you're adding a new cluster, click **Add Cluster**. On the new clusters page, click **New Google Container Cluster** and follow the instructions. Appropriate defaults have already been selected, but you can make changes if desired.
    * If you have an existing cluster, click **Sync Clusters**. 

## Step 4: Deploy “api-service”

Containerized applications are deployed to Kubernetes in pods. A pod is a group of one or more Docker containers along with options about how these containers are run. Pods are co-located and co-scheduled, and run in a shared context.

It is common practice to deploy pods that are composed of a single container and expose these pods using a Kubernetes service for external communication with other pods. Thus, while our Docker Composed application is composed of two services that are encapsulated by the same repository, we are going to deploy these services in separate pods. Doing so allows us to decouple these effectively independent services such that we can manage their scaling and operations independently of one another. 

A Kubernetes deployment facilitates an update of a pod to a desired state. Deployments are declared using objects that describe a desired state. These objects are passed to the Kubernetes Deployment controller which changes the actual state of the pod to the desired state at a controlled rate. 

A common way to describe desired state for a Kubernetes deployment is to use a yaml file. Here is the deployment specification we will use to deploy the “api-service”:

~~~
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: api-service
spec:
  replicas: 3
  template:
    metadata:
      labels:
        run: api-service
    spec:
      containers:
        - name: api-service
        - image: gcr.io/{GC_PROJECT}/api-service:{IMAGE_TAG}
~~~

This deployment specification instructs Kubernetes to create three pods, each with a running copy of the containerized “api-service”. Note the omission of the “namespace” field. This instructs Kubernetes to create the deployment at the default namespace.

1. Launch a Kubernetes deployment. Navigate to your project and click the rocket (**Deploy**) icon to initiate a deployment. 

1. Select your cluster and click **Create a new deployment**.

1. Paste the deployment specification into the yaml editor, give your deployment a description and deploy! 

This is the equivalent of running the kubectl command `kubectl create -f FILENAME`.

Note that Pipelines for Containers allows you to monitor the progress of your deployment and rollback immediately if necessary. It also displays the specification of your deployment in the deployment details page so you know exactly what was deployed, when it was deployed, where it was deployed, and by whom it was deployed.

After the deployment has completed, navigate to the **Clusters** dashboard to see the current state of your cluster. Information is included about Kubernetes pods, replica sets, deployments, and services. You can even view logs from your running pods in real time. Furthermore, you can easily download credentials for your cluster to connect to it from the command line using `kubectl`.

## Step 5: Expose “api-service”

Kubernetes Pods are ephemeral by default. They can be torn down and rebuilt by Kubernetes without warning. As such, while each pod has a private internal IP address within the cluster, one does not want to communicate directly over this address. Instead, it is recommended to create a Kubernetes Service to manage communication with pods. A Kubernetes Service is a long-lived object that encapsulates a logical set of pods and a policy by which to access them.

Much like Kubernetes deployments, Kubernetes services are defined declaratively and encapsulate a desired state that is passed to Kubernetes. It is common to use yaml for this specification.

Here is the service specification we will use to expose “api-service”:

~~~
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  ports:
    - port: 8001
      protocol: TCP
      targetPort: 8001
  selector:
    deployment: api-service
    run: api-service
  type: LoadBalancer
~~~

This is a simple specification that instructs Kubernetes to create a load balancer with an external IP that will listen to traffic over port 8001 and communicate with the container over TCP on its port 8001. Recall “api-service” is exposed on port 8001 via its Dockerfile, so launching this service should allows us to communicate with the pods that encapsulate “api-service” on our Kubernetes cluster. 

1. To launch a service, go to your project's page and click **Services**. 

1. Select your cluster and click **Create a new service**.

1. Paste in the service specification and click **Create Service**. 

You can now navigate to your cluster dashboard and inspect the newly created service. This dashboard updates dynamically and will show the external IP of the service once it is created. 

Once the external IP of the service has been created, you can communicate with the containerized “api-service” from outside of the cluster. Try to use your browser by going to `http://<YOUR_SERVICE_LB_IP>:8001/api/greeting?name=<YOUR_NAME>`.

## Step 6: Deploy “client-service”

Now that we have taken our Docker Composed application and exposed “api-service” on Kubernetes, it is time to deploy and expose its second service, “client-service”. In doing so we will also establish a mechanism for communication between “client-service” and “api-service” such that users could communicate with the “client-service”, which will in turn communicate with the “api-service” before returning a response to the user. 

Deploying “client-service” should be a breeze now that we’ve already deployed “api-service”. We will use the Pipelines deployment utility to deploy “client-service” with the following deployment specification:

~~~
apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: client-service
  spec:
    replicas: 3
    template:
      metadata:
        labels:
          run: client-service
      spec:
        containers:
        - name: client-service
          image: gcr.io/{YOUR_GOOGLE_COMPUTE_PROJECT}/client-service:{YOUR_IMAGE_TAG}
          env:
          - name: api
            value: http://{API_SERVICE_LOAD_BALANCER_IP}:8001
~~~

This deployment specification tells Kubernetes to create three pods of “client-service”. Each pod is disseminated with the environment variable “api” which given the “client-service” the endpoint through which it is to communicate with the “api-service”.

1. Launch a Kubernetes deployment. Navigate to your project and click the rocket (**Deploy**) icon to initiate a deployment. 

1. Select your cluster and click **Create a new deployment**.

1. Paste the deployment specification into the yaml editor, give your deployment a description and deploy! 

Once the deployment of “client-service” is complete we can verify that “client-service” is running by looking at the logs. 

## Step 7: Expose “client-service”

Now that both “client-service” and “api-service” are running on Kubernetes in distinct pods it is time to expose “client-service” by using a Kubernetes Service. We will use the following service specification to expose “client-service”:

~~~
  apiVersion: v1
  kind: Service
  metadata:
    name: client-service
  spec:
    ports:
    - port: 80
      protocol: TCP
      targetPort: 8080
    selector:
      deployment: client-service
      run: client-service
    type: LoadBalancer
~~~

This specification instructs Kubernetes to create a load balancer that listens on port 80 (the default port for http requests) and redirects traffic to the containerized “client-service” on port 8080. Recall that “client-service” is exposed in its Dockerfile on port 8080.

1. To launch a service, go to your project's page and click **Services**. 

1. Select your cluster and click **Create a new service**.

1. Paste in the service specification and click **Create Service**. 

You can now navigate to your cluster dashboard and inspect the newly created service. This dashboard updates dynamically and will show the load balancer IP of your newly created service in a few minutes. 

Now that “client-service” is exposed you can use your browser to navigate to the public load balancer IP of the service and interact with your containerized application. Try it by going to http://<YOUR_CLIENT_SERVICE_LOAD_BALANCER_IP>?name=<YOUR_NAME>. 

You can now interact with your application locally using Docker Compose, while you can scale and manage the different services of your application independently of one another on Kubernetes. 

The only thing that remains to be done is to automate this entire process. That is, on your next commit to your application repository, golang_docker_kubernetes, you would ideally like new images of your services to be built, tested, and uploaded to their registries and a Kubernetes deployment to be kicked off that applies the new images onto the correct pods such that consumers of your application can interact with the modified code. 

## Step 8: Create your pipelines

Container pipelines are a mechanism for defining automation onto Kuberentes. Container pipelines can be triggered by commits or pull requests on a specified repository and defined to proceed sequentially through automation steps. 

We will create two container pipelines, one for “api-service” and one for “client-service”. 

1. Navigate to your project's page and click **Pipelines**.

1. Click **Create Pipeline** and give your new container pipeline a name. 

1. Add a container to the pipeline. The container tells Pipelines for Containers which repository and branch to monitor for push and pull request events. Click **Add Containers**. 

1. Add a stage to your container pipeline. A stage is an encapsulation of a Kubernetes cluster at a specific namespace and deployment name. When a build succeeds in Puppet Pipelines after a push or pull request event on your specified repository, Pipelines will take the new Docker image generated from the build and will apply it to the deployment specified by the stage in the pipeline. All the other configurations of the deployment, except for the new image, will be preserved.

1. Check **Auto Deploy on Build Success** to tell the container pipeline to proceed to the stage after a successful build. Note that you can add n stages to the pipeline to facilitate deploying to staging environments before taking your application all the way to production. 

1. Repeat the four steps above to add a container pipeline for “client-service”. The only difference should be the the pipeline for “client-service” will point to a stage at deployment “client-service” on namespace “default”.

## Step 9: Commit to your application and watch automation take place

1. To see the automated process in action, modify line 45 of greeting_handler.go in “api-service”: 

~~~
return "Hello " + name + " you are awesome!"
~~~

1. Push this change to your repository, then return to Pipelines for Containers and watch your container pipelines doing work! Once the builds are complete, you will see deployments being kicked off to update the pods that encapsulate “api-service” and “client-service”.

1. Once your deployments are successful, navigate to your exposed “client-service” at <YOUR_CLIENT_SERVICE_LOAD_BALANCER_IP>?name=<YOUR_NAME> and see that your new images were in fact applied. 

You have now taken a simple Docker Composed application comprised of two services and deployed that application onto Kubernetes in a way that makes scaling and managing the application a breeze. Moreover, you have set up complete automation around your Docker Composed application such that subsequent commits to the master branch of your repository will trigger a cycle of building, testing and uploading new Docker images to your Docker registry and then apply these new images to your Kubernetes pods using Kuberentes deployments. 



 