---
layout: default
title: "Kubernetes deployment specification"
--- 

Kubernetes is quickly becoming the tool of choice to facilitate the deployment, scaling, and management of containerized applications. 

Containerized applications are deployed to Kubernetes by declaratively passing a desired state to the Kubernetes Deployment Controller. When given a new declaration of desired state, Kubernetes proceeds to modify the system from the current state to the desired state. 
 
It is common to use a yaml file as a declaration of desired state. In this article we will examine the various configurations one can specify in a yaml Kubernetes deployment specification.

## Basic deployment concepts

The most basic Kubernetes specification yaml will look something like this:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

Assuming the configuration above is saved in a file called “deployment.yml”, initiating a deployment to Kubernetes is as easy as:

~~~
$ kubectl create deployment -f deployment.yml
~~~

Let’s go through this specification to understand exactly what desired state is being communicated to Kubernetes:

#### apiVersion

apiVersion is an optional String value that describes the versioned schema of the deployment object defined by the yaml specification file. While apiVersion is not strictly required, it is good practice to specify the exact version of the deployment object defined in your Kubernetes deployment specification to avoid serialization issues by the Kubernetes controller.  

In the example above we are using the “extensions/v1beta1” deployment object schema. 

#### Kind

Kind is an optional String value that describes the resource defined by the yaml specification file. Kubernetes can infer kind based on the endpoint to which the specification is submitted. However, it is good practice to specify kind in order to allow human readers of the specification to quickly discern what object the specification is describing. 

In the example above we are describing a deployment object, so kind is set to “Deployment”.

#### metadata

metadata is an object containing important information about a persisted Kubernetes entity. There are many attributes than can be specified as metadata. We will explore them in greater detail later in this article. 

#### metadata.name

metadata.name is a required String when creating or modifying a Kubernetes deployment. Each deployment in Kubernetes is scoped to a namespace. Deployments are uniquely identified within a namespace by their name. As a result, multiple deployments in the same namespace cannot share the same name. 

In the example above we are naming our deployment “nginx-deployment”. By naming our deployment we can use kubectl to query for it by, for example, using the following commands:

~~~
  $ kubectl get deployments nginx-deployment
  $ kubectl describe deployments nginx-deployment
~~~

See these resources to further educate yourself about Kubernetes namespaces and names.

Note that to deploy to a different Kubernetes namespace than the “default” namespace, you can simply specify the metadata.namespace String attribute. For example (changed field is highlighted for clarity):

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
    namespace: some-namespace
  spec:
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

#### metadata.namespace

The metadata.namspace  field specifies which namespace the deployment belongs to. Namespaces allow you to create separations within your cluster. Typically, objects can only communicate with other objects if they share a namespace. If the namespace field is omitted from your specification, the namespace 'default' is used.

#### spec

The spec object is the real meat of the deployment specification yaml. It is where you define the actual desired behavior of the Kubernetes Deployment. You should always include a spec to have any meaningful action taken by Kubernetes, though the spec object is not strictly required by the Kubernetes API. 

The spec object contains many fields. We will describe each of these fields later in this article. Of all the fields of the spec object only spec.template is required. 

#### spec.template

spec.template is a required object that describes the Pods Kubernetes should create as part of the deployment. Containerized applications are ran on Kubernetes in Pods, logical groupings which share storage and management instructions. Pods are always co-located and co-scheduled, and run in a shared context. A Pod can contain one or more containerized applications. 

One can query Pods by, for example, running:

~~~
  $ kubectl get pods
  $ kubectl get pods {POD_NAME}
  $ kubectl describe pods {POD_NAME}
~~~

#### spec.template.spec

spec.template.spec is an object within spec.template which provides the explicit instructions for the desired behavior of a Pod. It contains many fields which will be described later in this article. Of all its fields, only the spec.template.spec.containers is required.

#### spec.template.spec.containers

spec.template.spec.containers is an array of objects that must include at least 1 container. Each of the containers in this array describes the behavior of a containerized application within a Pod. It is not uncommon for Pods to only contain one container, though it is possible to specify any number of containers. 

In the example above we are only deploying 1 container.

#### spec.template.spec.containers[i].name

spec.template.spec.containers[i].name is a required String. Each container within a Pod must have a unique name. Names must conform to a DNS_LABEL defines as a string, no more than 63 characters long, that conforms to the definition of a “label” in RFCs 1035 and 1123. This is captured by the regex [a-z0–9]([-a-z0–9]*[a-z0–9])?

In the example above we have named our container “nginx”

#### spec.template.spec.containers[i].image

spec.template.spec.containers[i].image is an optional String that describes which Docker image should run in the deployed container. Because of this, when deploying a containerized application that is defined as a Docker image, one should always include this field. 

In the example above we are deploying the public copy of the nginx image with tag 1.7.9 stored on DockerHub. 

We could likewise deploy private images by specifying a private Docker registry. For example here is a modified spec pointing to an nginx image stored on Pipelines' private Google Container Registry (changed field is highlighted for clarity):

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    template:
      spec:
        containers:
        - name: nginx
          image: gcr.io/Pipelines/nginx:1.7.9
~~~

Applying changes to a Kubernetes deployment specification is as easy as doing:

~~~
$ kubectl apply -f deployment.yml
~~~

As we see, a lot is communicated to Kubernetes with only a few lines of yaml configuration.

## Advanced deployment options

In this article we are going to add and explain several advanced deployment configurations options we can add to this simple specification. Each change in the specification will be highlighted to allow the reader to more easily identify where the change took place in the yaml. For example, if I were to change the name of the deployment I will present the yaml as follows:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: my-changed-deployment-name
  spec:
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

### How to control how many pods are created in a deployment?

In order to control how many Pods are created as part of a Kubernetes deployment one must specify the spec.replicas field. The omission of this field tells Kubernetes to create only one Pod. 

#### spec.replicas

spec.replicas is an optional integer that tells Kubernetes how many Pods to create during a deployment. Modifying this field is an easy way to scale a containerized application. Here is a modification to our yaml configuration that scales the deployment to 3 Pods:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    replicas: 3
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

An easy way to scale up and down from the command line is to use the kubectl scale command. For example:

~~~
$ kubectl scale --replicas=3 deployment nginx-deployment
~~~

or one can also scale by referencing a deployment specification file directly:

~~~
$ kubectl scale --replicas=3 -f my-deployment-spec.yml
~~~

### How to control the strategy with which Kubernetes replaces existing pods?

The spec.strategy object tells Kuberentes which strategy to employ when replacing existing Pods with new Pods

#### spec.strategy

spec.strategy is an optional object which explicitly tells Kuberentes how to replace existing Pods with new Pods. At the time of this publication, there are two deployment strategies from which to choose:

1. Rolling Update Deployment Strategy

This is the strategy used by default in Kubernetes when the spec.strategy object is omitted from the deployment specification. The Rolling Update strategy allows Kuberentes to update a service without facilitating an outage by proceeding to update Pods one at a time. Omitting spec.strategy is the equivalent of specifying the following:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
    replicas: 3
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

where spec.strategy.rollingUpdate.maxUnavailable is the maximum number of pods that can be unavailable during the update, and spec.strategy.rollingUpdate.maxSurge is the maximum number of pods that can be scheduled above the desired number of pods. Hence, with the above configuration we are telling Kubernetes to update the Pods one at a time, in such a way that no more than 1 Pod is ever unavailable, and that no more than 4 Pods are ever scheduled. 

Note that spec.strategy.rollingUpdate.maxUnavailable and spec.strategy.rollingUpdate.maxSurge can also be defined as a percentage of all Pods. For example:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 10%
        maxSurge: 10%
    replicas: 3
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

2. Recreate deployment strategy

In a Recreate deployment all existing Pods are killed before new ones are created. Thus, a Recreate deployment is a way a more aggressive deployment action that should likely be reserved for necessitating occasions. Here is an example of our deployment specification using a Recreate deployment:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    strategy:
      type: Recreate
    replicas: 3
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

### How to dictate when a pod is available after an update?

spec.minReadySeconds can be used to give Kubernetes an indicator to inspect before it is to consider a Pod to be available. 

#### spec.minReadySeconds

spec.minReadySeconds is an optional Integer that describes the minimum number of seconds for which a new pod should be ready without any of its container crashing, for it to be considered available. The omission of this field defaults spec.minReadySeconds to 0 seconds. Thus, with the mission of this field Pods are considered available as soon as they have been updated. 

Here is a modification to our deployment specification that requires Kubernetes to wait and see that the all of the Pods containers are running without crashing for 10 seconds before making the Pod available:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    minReadySeconds: 10
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
    replicas: 3
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

### How to control how many revisions of a given deployment are kept by Kubernetes?

Kubernetes allows for easy and intuitive rollbacks by maintaining pointers to old Replica Sets. Replica Sets are used by Kubernetes Deployments to orchestrate pod creation, deletion and modification. However, it can be potentially expensive and unnecessary to main too many copies of old Replica Sets, particularly as your containerized application has gone through many iterations that will make it unlikely you will roll it back to a very old previous Replica Set. 

spec.revisionHistoryLimit an optional integer attribute you can use to tell Kuberneres explicitly how many old Replica Sets to retain at any given time. 

Here is an example of usage that tells Kubernetes to maintain references to only the 5 previous Replica Sets of our deployment:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    revisionHistoryLimit: 5
    minReadySeconds: 10
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
    replicas: 3
    template:
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

## Labels

Labels are key/value pairs that are attached to Kubernetes objects, such as Deployments and Pods. Labels are used to specify attributes that are meaningful to users, but which are not directly meaningful to the core Kubernetes system. 

Labels are often used to organize and to select groups of objects that are logically related. For example, a common usage of labels is to select all the Pods with a given label. Here is how one would query for all Pods with a given label using kubectl:

~~~
$ kubectl get pods -l label_key=label_value
~~~

Of course, more complex queries with multiple label values can be specified:

~~~
$ kubectl get pods -l label_key1=label_value1,label_key2=label_value2 ...
~~~

There is a plan for Kubernetes to eventually index and reverse-index labels for efficient queries and watches, so their use is highly recommended for logical grouping of related objects.

### When can labels be created and modified?

Labels can be attached to objects when they are created, or added to objects during any modification. Note that within an object, no two labels can share the same key. 

### How do I add labels to my Kubernetes deployment specification?

One can use spec.template.metadata.labels to add labels to a deployment specification spec.template.metadata.labels

spec.template.metadata.labels is an optional array of key/value pairs. Here is an example of two labels being added to our example deployment specification:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    revisionHistoryLimit: 5
    minReadySeconds: 10
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
    replicas: 3
    template:
      metadata:
        labels:
          app: nginx
          deployer: distelli
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

Note that the label keys “app” and “deployer”, as well as the values “nginx” and “distelli”, are completely arbitrary. Any key/value pairs can be used as labels as long as they follow the syntax rules specified by Kubernetes.

After applying this deployment querying for the pods associated with the deployment is as simple as:

~~~
$ kubectl get pods -l app=nginx,deployer=distelli
~~~

## Label selectors

Labels, unlike Kubernetes names and UIDs, are not unique and it is expected that labels will be used on multiple objects as a way to easily group objects. 

Label selectors are constructs that can be used by users to identify groups of objects that share labels.

There are two types of label selectors: equality-based selectors and set-based selectors.

1. Equality-based selectors

Equality-based Selectors use = to symbolize equality and != to symbolize inequality. Matching objects must satisfy all of the specified label constraints, though they may have additional labels as well.

Thus, the example above is an equality-based selector:

~~~
$ kubectl get pods -l app=nginx,deployer=distelli
~~~

This selector tells Kubernetes to retrieve all Pods with the label that has key "app" with value "nginx" AND key "deployer" and with value "distelli".

But the partial equality selectors below would also work for the pods associated with the deployment we have been exploring:

~~~
$ kubectl get pods -l app=nginx
~~~

~~~
$ kubectl get pods -l deployer=distelli
~~~

Conversely, the selector below tells Kubernetes to get all pods that do not have a label with key "app" and value "nginx":

~~~
$ kubectl get pods -l app!=nginx
~~~

2. Set-based selectors

set-based selectors allow users to query objects based on their inclusion or exclusion from a set. There are four variations on these selectors:

**in** - select all objects in a collection that have the specified labels and values. For example, here is the command to select all pods with key "app" and value "nginx" or value "test" and key "deployer" with value "distelli":

~~~
$ get pods -l 'app in (nginx, test), deployer in (distelli)'
~~~

**notin** - select all objects in a collection that do not have the specified labels and values. For example, here is a query to select all pods that do not have a label with key "app" and value "nginx":

~~~
$ get pods -l 'app notin (nginx)'
~~~

**label** - select all object that have the specified label(s), irrespective of value. For example, here is a query to select all pods that have a key of "app" with any value:

~~~
$ get pods -l 'app'
~~~

**!label** - select all object that have do not have specified label(s), irrespective of value. For example, here is a query to select all pods that do not have a key of "app":

~~~
$ get pods -l '!app'
~~~

### How can I use label selectors in a deployment specification to tell my deployment to only target pods with certain labels?

Use the spec.selector object in the deployment specification yaml

#### spec.selector

spec.selector is an optional object that tells the Kubernetes deployment controller to only target pods that match the specified labels. Thus, to only target pods with the labels "app" and "deployer" we can make the following modification to our deployment yaml:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
  spec:
    revisionHistoryLimit: 5
    minReadySeconds: 10
    selector:
      matchLabels:
        app: nginx
        deployer: distelli
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
    replicas: 3
    template:
      metadata:
        labels:
          app: nginx
          deployer: distelli
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~

### What are annotations?
 
annotations are labels (key-value pairs) that contain arbitrary non-identifying metadata. That is, you should add annotations to a deployment specification in order to provide the deployment object with relevant information, but not information that you would query for the object with. Common annotations include release version, deployment description etc. 

### How do I add annotations to my deployment specification?

Use metadata.annotations to add annotations to the deployment object or spec.template.metadata.annotations to add annotations to the pods. Here is an example of adding annotations to our deployment spec for a deployment description and an image release version:

~~~
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: nginx-deployment
    annotations:
      description: new deployment
  spec:
    revisionHistoryLimit: 5
    minReadySeconds: 10
    strategy:
      type: RollingUpdate
      rollingUpdate:
        maxUnavailable: 1
        maxSurge: 1
    replicas: 3
    template:
      metadata:
        labels:
          app: nginx
          deployer: distelli
        annotations:
          version: v1
      spec:
        containers:
        - name: nginx
          image: nginx:1.7.9
~~~
