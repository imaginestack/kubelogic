# Kubernetes Design Patterns

Design patterns are the formalization of best practices for everyday problems. Using design patterns in everyday, professional life creates a common language and communication platform for you to work on. In real life, seasoned engineers do not explain how to convert one interface into another; instead, they decide to implement an adapter. Design patterns hide the complexity and details of communication and create a common platform. In addition, converting business requirements into code is more comfortable with the accumulated knowledge of design patterns.

Kubernetes is the uprising and prominent open source container orchestration system, designed by Google. Its fundamental features include automation, scaling, and scheduling of containerized applications. To have a scalable and reliable cloud-native application, Kubernetes is a crucial part of your toolset. All levels of companies, from start-ups to large enterprises, are using Kubernetes to install and manage cloud-native applications.

This paradigm shift in software development began by creating microservices instead of chunks of large software systems. The "new" best practices have aligned with the de facto cloud-native orchestration tool, Kubernetes. Throughout this book, Kubernetes design patterns and extension capabilities will be presented. The book starts by explaining best practices to show how to create Kubernetes-native applications. Following that, accessing Kubernetes itself programmatically and enriching the best orchestration tool ever created will be explained. Finally, Kubernetes itself will be extended with a higher level of automation. At the end of the day, you will have the technical knowledge and hands-on experience to not only create applications to run on Kubernetes but also extend the system itself.

In this first section, Kubernetes design patterns will be presented, starting with the fundamentals of design patterns. Following that, you will build Kubernetes solutions using structural patterns, assemble systems with behavioral patterns, and finally, install applications according to the deployment strategies.


-   Define the fundamentals of design patterns
-   Explain the classification of patterns
-   Use Kubernetes design patterns to solve real-life problems
-   Build solutions using structural patterns
-   Assemble complex systems with behavioral patterns
-   Install applications with deployment strategies


## Software Design Patterns

There are two main uses of design patterns. First, design patterns create a common platform for developers with their terminology. For example, during a technical discussion, let's assume that a design decision is made to use a single instance of a component. All other developers, at least the ones that are aware of design patterns, will not need any further information considering the properties of the singleton pattern. Although this looks trivial, it is an enrichment of communication, with the best practices of technical expertise. Secondly, knowing and leveraging best practices in engineering makes it easier to advance rapidly. Let's imagine that you are designing a car – you should always start by inventing the best wheel possible first. This makes the process faster and eliminates the gains of learning from past mistakes.

### Classification of Software Design Patterns

Design patterns are classified in three ways. With new technologies and programming languages always emerging, new groups are proposed, but the main idea of classification remains the same – the interaction between their controllers and other applications:


![Design Patterns Classification](https://static.packt-cdn.com/products/9781789619270/graphics/assets/0937be66-17ef-44fb-b90c-2a528ff58682.png)

To sum this section up, design patterns are formalized best practices that have roots in both real-life and software design. They create a common communication platform for developers, and they are a valuable source of knowledge as a collection. In the following section, design patterns for Kubernetes are presented as best practices for creating, managing, and deploying modern cloud-native applications.


## Kubernetes Design Patterns

The evolution of microservices and container technologies has changed the way software applications are designed, developed, and deployed. Nowadays, modern cloud-native applications focus on scalability, flexibility, and reliability in order to meet business requirements. For instance, the scalability of an application with thousands of instances running in a reliable manner was not a concern 10 years ago. Similarly, self-awareness and self-healing features were out of scope when there was no orchestrator for these issues, such as Kubernetes.

New requirements and cloud-native characteristics unveil their own best practices and design patterns. Extensive use and the adoption of Kubernetes by all sorts of companies, including start-ups and large enterprises, made it possible to formalize and collect best practices. In this section, widely known and essential design patterns and deployment strategies in Kubernetes will be described. With the help of these patterns, you'll be able to make use of the best practices that have been formalized by Kubernetes itself.

**Structural Patterns**

Structural patterns are focused on the composition of building blocks to create higher-level complex resources. In microservice architecture, applications are packaged and deployed as containers. This approach makes it easier to scale applications with less overhead and more isolation. However, this makes it difficult to schedule and run related containers side-by-side, or sequentially in a cluster with thousands of nodes. For instance, if you want to run your frontend and backend containers together in a cluster, you need to find a mechanism so that you can always schedule them to the same nodes. Likewise, if you need to fill in configuration file templates before starting your application, there is a need to ensure that configuration handler containers are running before the application is.

In Kubernetes, containers are the building blocks that are encapsulated in pods. As a container orchestrator, Kubernetes provides built-in functionalities for organizing containers within pods. In this section, the sidecar and initialization structural design patterns for Kubernetes will be explained.

Pods are the smallest deployable resources in Kubernetes, and they consist of one or more containers sharing resources. Pod containers are always scheduled to the same node so that they can share resources such as networking and storage. Further information about the pod concept is available in the official documentation of Kubernetes: [https://kubernetes.io/docs/concepts/workloads/pods/pod](https://kubernetes.io/docs/concepts/workloads/pods/pod).

**Sidecar Pattern**

Modern software applications often require external functionalities such as monitoring, logging, configuration, and networking. When these functionalities are tightly integrated into the application, they can run as a single process. However, this violates the isolation principle and creates an opportunity for a single point of failure. With this idea, containers in cloud-native applications are expected to follow the Unix philosophy:

-   Containers should have one task
-   Containers should work together
-   Containers should handle text streams

The **single point of failure (SPOF)** is a component within a system where its failure can cause all systems to fail. To have reliable and scalable cloud-native applications, SPOFs are undesirable, and system designs should be checked for their existence.

The Unix philosophy focuses on designing a small operating system with a clean service interface. To have such a system, simple, precise, clear, and modular software development should be undertaken, taking into consideration the developers and maintainers. Besides, the philosophy emphasizes that you should compose subsystems instead of creating a big, monolithic design.

With this idea, it is a conventional approach to separate the main application and run a couple of sidecars attached, which are provided for extra functionality. The main advantages of using a sidecar are as follows:

-   They have independent programming languages and runtime dependencies
-   They monitor the main application closely and minimize latency
-   They extend black box applications

In the following activity, a web-based game will be installed in Kubernetes. As expected, there should be at least one container running the web server. However, there is an additional requirement of continuous source code synchronization. With the Unix philosophy, it is expected to make containers with only one primary task. They should also work independently and together to achieve the necessary requirements. Finally, these containers should update their statuses, informing the console as log lines. All three of these points are covered in the following activity.

**Activity: Running a Web Server with Synchronization**

**Scenario**

You are assigned the task of making a Kubernetes installation for a web-based 2048 game. However, the game is still in development and developers push frequent changes throughout the day. In this installation, the game should be frequently updated to include the recent changes.

**Aim**

With the successful deployment, there should be a pod running in Kubernetes with two containers. One of the containers should serve the game and the other container, namely the sidecar container, should continuously update the source code of the game. Using Kubernetes, you need to create a cloud-native solution where the server and synchronization tasks are working together, but not depending on each other.

Design patterns and corresponding business requirements could seem artificial and only software-related. However, both problems and solutions have roots in real life. For instance, the singleton pattern is proposed as a best practice for implementing a configuration manager. With the same approach in mind, the adapter pattern is proposed as a best practice to work with both versions of the APIs. As its name implies, it is a similar approach in real-life to using electrical adapters to work with the different plug and socket types in various countries. As these examples indicate, software design patterns and the ideas behind them all come from real-life experiences.

**Prerequisites**

Use the Kubernetes Downward API to collect runtime information.

**Steps for Completion**

1.  Create a pod definition with one container:
    -   Define the environment variables from the Downward API.
    -   Create a shell script to write the environment variables.
2.  Deploy the pod.
3.  Check the status of the pod.
4.  Check the logs of the container.


# Deployment Strategies

Designing and developing cloud-native applications with the microservice architecture is essential for reliable and scalable applications of the future. Likewise, deploying and updating applications in the cloud is as critical as design and development. There are various techniques for delivering applications, and therefore choosing the right setup is essential to leverage the impact of change on the consumers. Using the right subset of Kubernetes resources and choosing an appropriate deployment strategy, scalable and reliable cloud-native applications are feasible.

In this section, the following deployment strategies are presented, and you are expected to complete these exercises so that you can see the Kubernetes resources in action:

-   Recreate strategy
-   Rolling update strategy
-   Blue/green strategy
-   A/B testing strategy

# Recreate Strategy

The recreate strategy is based on the idea of closing old version instances and then creating the next version's. With this strategy, it is inevitable to have downtime, depending on both the shutdown and start duration of applications. In Kubernetes, the recreate strategy can be used for creating deployment resources with the strategy of recreate.

The rest of the operations are handled by Kubernetes. Under the hood, the steps of the recreate strategy are as follows:

1.  Requests from users are routed to **V1** instances by using a load balancer:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/3fa1ecdc-f978-421f-9b08-34e379187f7c.png)

2.  **V1** instances are closed, and downtime has started since there is no available instance:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/9a6d3f60-4721-4ddd-80ed-00a168eebd3b.png)

3.  **V2** instances are created; however, they are not serving the requests until they are ready:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/38e97134-fe36-4db0-8736-502a2f511080.png)

4.  Requests from users are routed to **V2** instances:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/d495b280-8f6c-4d74-98ee-befcbcdb1632.png)

The primary benefits of the recreate strategy are that it's straightforward and doesn't have any overhead processes. Besides this, the instances are completely renewed with every update, and there is no time when two versions are running together. However, the downside of this strategy is an inevitable downtime during the update.

# Deploying the Application Using the Recreate Strategy

Let's begin by following these steps:

1.  Create the deployment with the following command:

```markup
 kubectl apply -f recreate.yaml
```

2.  In a separate terminal, watch for the deployment changes and wait until all three are available:  
    

```markup
kubectl get deployment recreate -w 
```

3.  Update the version of the deployment:

```markup
kubectl patch deployment recreate -p '{"spec":{"template":{"spec":{"containers":[{"name":"nginx", "image":"nginx:1.11"}]}}}}'
```

4.  In the terminal that we opened in _Step 2_, it is expected that you should see the deletion of the pods and the downtime, where Available reaches zero. Afterward, the creation of new instances can be tracked, where Available increases from 0 to 3:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/d454aa26-432c-4254-95b7-f6bce2eeeabd.png)

5.  You can run the following command for cleanup:  
    

```markup
 kubectl delete -f recreate.yaml
```

You will notice that the available replicas will gradually decrease to 0, and then increase to 3 as the new pods are created.

By using the recreate strategy, Kubernetes ensures that there is no moment when two versions of the application are running together. However, there will be a short downtime during the update when no pods are available.

# Rolling Update Strategy

The rolling update strategy, which is also known as incremental or ramped, is based on the idea of slowly rolling out a version by replacing the previous ones. In this strategy, firstly, a pool of applications is running behind a load balancer. Then, new version instances are started, and when they are up and running, the load balancer redirects requests to the new instances. At the same time, instances from the previous versions are shut down. In Kubernetes, the rolling update strategy is the default strategy in deployments, so any update on the _deployment_ is already implementing the rolling update strategy.

While Kubernetes handles this, the steps of the rolling strategy can be tracked as follows:

1.  Requests from users are routed to **V1** instances by using a load balancer:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/0707a0f0-02c2-415c-9215-a3b8c0163a68.png)

2.  **V2** instances are created, and users are directed to them. At the same time, **V1** instances are deleted. During this stage, both versions are running and serving requests:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/a5f35b30-2119-44ad-b308-bcfa89c94766.png)

3.  Creating **V2** instances and the deletion of **V1** instances is done one at a time until there are no **V1** instances left:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/9462c465-a2ff-48cd-a6c8-3f107f38e8b5.png)

4.  Finally, all requests from all users are routed to **V2** instances:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/1bf167fe-323b-4530-9eff-3680ba2479b5.png)

The main advantage of the rolling update strategy is that it is easy and is the default approach of Kubernetes. It is also beneficial for a slow release of new versions by balancing the load during the startup of new instances. On the other hand, Kubernetes automatically handles rollout and rollback, and the duration of these operations are not known. One of the most complicated problems in rolling updates is that two versions are running at the same time in the cluster, and the traffic is not under control.

# Deploying an Application Using the Rolling Update Strategy

You are running a high-available application on Kubernetes that needs a deployment strategy to handle updates. This application, let's say, the frontend of your client's company, should always be available so that downtime is out of the question. Multiple versions of the applications could be working together at any time; however, the client does not want to cover the cost of extra resources during the update. We'll run an application with the rolling update deployment strategy so that updates will be handled by Kubernetes by incrementally creating new instances and deleting old instances, one after another. Let's begin by following these steps:

1.  Create the deployment with the following command:

```markup
kubectl apply -f rolling.yaml
```

2.  In a separate terminal, start a cURL instance in the Kubernetes cluster:  
    

```markup
kubectl run curl --image=tutum/curl --rm -it
```

3.  When the Command Prompt is ready, watch for the version of the deployment by using an HTTP request:  
    

```markup
while sleep 0.5; do curl -s http://rolling/version | grep nginx; done
```

4.  Update the version of the deployment with the following command:  
    

```markup
kubectl patch deployment rolling -p '{"spec":{"template":{"spec":{"containers":[{"name":"nginx", "image":"nginx:1.11"}]}}}}'
```

5.  In the terminal that we opened in _Step 2_, it is expected that we should see Kubernetes handle an incremental change of versions. During the update, there is no interruption of the service. However, both versions are alive and serving requests as 1.10.3 and 1.11.13, and are used interchangeably:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/d8d68db0-6c48-4443-a634-9be83d2c51cd.png)

6.  For cleanup, stop the cURL command with _Ctrl_ + _C_ and exit from the pod by writing exit. The pod will be deleted by Kubernetes upon exit. Run the following command:

```markup
kubectl delete -f rolling.yaml
```

  
You can find the rolling.yaml file at: [https://goo.gl/eo8cJw](https://goo.gl/eo8cJw).  

# Blue/Green Strategy

The blue/green strategy is the idea of having two active production environments, namely blue and green. The blue environment is active and serving requests. The green environment has the new version, and it is being tested for the update. When the tests are completed successfully, the load balancer is switched from blue to green instances. In Kubernetes, blue/green deployment is handled by installing both versions and then changing the configuration of a service or resource.

1.  The main steps of the blue/green strategy can be defined as follows:Both the **V1** and **V2** instances are deployed, and the load balancer is configured for **V1** instances:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/af597763-50de-41f6-ba77-492ff0ccd5bd.png)

2.  After testing, the load balancer is configured for route **V2** instances:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/aa4fe476-3a42-40f5-a90c-243e8b840c04.png)

Advantages of the blue/green strategy include instant rollout and rollback, and no version mismatch during the update. On the other hand, a drawback of this strategy is that you are using double the resources since two environments are maintained. It should be noted that exhaustive end-to-end testing of applications should be handled before using this strategy.  

# Deploying an Application Using the Blue/Green Strategy

You are running a high-available application on Kubernetes that needs a deployment strategy to handle updates. This application, for example, a mortgage calculation engine API, has different versions, which result in different calculation results. Therefore, the client requires extensive testing before release and instant switches between the versions. We'll run an application with blue/green strategy so that both versions are running, and Kubernetes service handles instant switch of version. We'll run an application with blue/green strategy so that both versions are running, and Kubernetes service handles instant switch of version. Let's begin by performing the following steps:  

1.  Create both versions of the deployment and the common service with the following command:  
    

```markup
kubectl apply -f blue-green.yaml
```

2.  Check that both versions are deployed on the cluster with the following command:

```markup
kubectl get pods
```

You should see the following output:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/f3d121b9-05cb-4d96-b5a1-c5fb200b00bf.png)

3.  In a separate terminal, start a cURL instance in the Kubernetes cluster:  
    

```markup
kubectl run curl --image=tutum/curl --rm -it
```

4.  When the command prompt is ready, watch for the version of the deployment by using an HTTP request:

```markup
while sleep 0.5; do curl -s http://blue-green/version | grep nginx; done
```

5.  Update the service to route traffic to the new version with the following command:  
    

```markup
kubectl patch service blue-green -p '{"spec":{"selector":{"version":"1.11"}}}'
```

6.  In the terminal that we opened in Step 3, we should see the Kubernetes service handling an instant change of versions without any interruption:  
    

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/dfd8eddc-0b64-47c8-9d92-f6995b3f476d.png)

7.  For cleanup, stop the cURL command with _Ctrl_ + _C_ and exit from the pod by writing exit. The pod will be deleted by Kubernetes upon exit. Run the following command:  
    

```markup
kubectl delete -f blue-green.yaml
```

# A/B Testing Strategy

The A/B testing strategy is based on the idea of consumer separation and providing different subsets of functionalities. A/B testing allows you to run multiple variants of functionality in parallel. With the analytics of user behavior, users can be routed to a more appropriate version. The following is a sample list of conditions that can be used in order to scatter traffic:

-   Cookies
-   Location
-   Technology, such as the browser, screen size, and mobility
-   Language  
    

In the following image, both versions are installed, and users are routed based on their technology characteristics, that is, mobile or desktop:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/4967e855-c0ea-4645-a614-3e5e321fbc9f.png)

The main advantage of the A/B testing strategy is that you have full control over traffic. However, distributing the traffic requires an intelligent load balancer other than the regular Kubernetes services. Some of the popular applications for this are as follows:

-   Linkerd
-   Traefik
-   NGINX
-   HAProxy
-   Istio  
    

Linkerd, Traefik, NGINX, and HAProxy are data plane applications that focus on forwarding and observing network packages between service instances. On the other hand, Istio is a control plane application that focuses on the configuration and  
management of proxies that route traffic.

# Deployment Strategies Summary

Before choosing a deployment strategy, it should be taken into consideration that there is no silver bullet to solve all production environment requirements. Therefore, it is crucial to check and compare the advantages and disadvantages of strategies and choose the most appropriate one:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/701f9765-e455-4e84-8f19-ca2ff8086047.png)
# Solutions

This section contains the worked-out answers for the activities present in each lesson. Note that in case of descriptive questions, your answers might not match the ones provided in this section completely. As long as the essence of the answers remain the same, you can consider them correct.


### Activity Solutions


#  Running a Web Server with Synchronization

In the sidecar.yaml file, pod definition with two containers, namely server and sync, is provided. In the server container, httpd serves the source volume on port 80. In the sync container, git runs with every 30 seconds to synchronize the source volume. These two containers work independently; however, they are sharing the source volume to achieve file synchronization:  

```markup
apiVersion: v1kind: Podmetadata:       name: sidecarspec:     containers:...volumes:- emptyDir: {}    name: source 
```

Follow these steps to get the solution:  

1.  Create the pod with the following command:  
    

```markup
 kubectl apply -f sidecar.yaml 
```

2.  Check whether the pod is ready with the name sidecar:  
    

```markup
 kubectl get pod sidecar 
```

3.  When the pod is ready, check the logs of synchronization sidecar container:  
    

```markup
 kubectl logs sidecar -c sync
```

4.  Forward the server port of the pod to localhost with the following command:  
    

```markup
 kubectl port-forward sidecar 8000:80
```

5.  Open localhost:8000 in the browser. It is expected to see a 2048 game.  
    

# Activity: Running a Web Server after Content Preparation

In the init-container.yaml file, pod definition with one initialization and one main container, namely content and server, is provided. In the content container, "Welcome from Packt" is written into the index file. In the server container, nginx serves the source volume on port 80. These two containers work independently; however, they are sharing the workdir volume to achieve file preparation:  

```markup
apiVersion: v1kind: Podmetadata:      name: init-containerspec:     initContainers:-...volumes:- name: workdir    emptyDir: {} 
```

Follow these steps to get the solution:  

1.  Create the pod with the following command:  
    

```markup
kubectl apply -f init-container.yaml
```

2.  Check the state of the initialization container:  
    

```markup
kubectl describe pod init-container
```

3.  When the pod is running, forward the server port of the pod to localhost with the following command:  
    

```markup
kubectl port-forward init-container 8000:80 
```

4.  In another terminal, check the content of the server with the following command:  
    

```markup
curl localhost:8000
```

5.  Run the following command for cleanup:  
    

```markup
kubectl delete deployment go-client 
```

# Activity: Injecting Data into Applications

In the inject.yaml file, there is a pod definition with one container that logs its runtime information in every 10 seconds. Resource requests and limits are defined for memory and cpu for utilization and performance information.  
Environment variables are defined with valueFrom blocks which shows that values are filled during runtime:  

Follow these steps to get the solution:  

1.  Create the pod with the following command:  
    

```markup
kubectl apply -f inject.yaml
```

2.  Check whether the inject pod is ready:  
    

```markup
kubectl get pod inject
```

3.  When the pod is ready, check the logs:  
    

```markup
kubectl logs inject
```


#  Client Libraries

Following are the activity solutions for this section.  

# Activity: Using the Kubernetes Go Client inside the Cluster

1.  Create a deployment with the Docker image of the example client from the previous exercise:  
    

```markup
kubectl run go-client --image=onuryilmaz/k8s-clientexample:go
```

2.  Wait until the pod is running by using the following command:  
    

```markup
kubectl get pods -w
```

3.  Get the logs of the deployment pod with the following command:  
    

```markup
kubectl logs $(kubectl get pods --selector run=go-client -o jsonpath="{.items[0].metadata.name}")
```
# Kubernetes Client Libraries

Kubernetes provides multiple options for creating applications with the Kubernetes API. These options include tools such as kubectl, helm, kops, and kubeadm; and client libraries, which are officially supported or community-maintained. However, it is essential that you know the capabilities and boundaries of the clients in order to create applications that interact with Kubernetes.

In this section, you will learn how to access the Kubernetes API directly and use Kubernetes client libraries. Firstly, direct access to the Kubernetes API will be explained, and responses from the Kubernetes API will be demonstrated. Following that, official and community-maintained libraries will be given, including detailed information and example applications. Throughout this section, you'll develop applications that connect to the Kubernetes API, inside and outside the cluster.  

By the end of this section, you will be able to:

-   Evaluate the Kubernetes API request and response style
-   Connect to the Kubernetes API using HTTP
-   Find and use official client libraries
-   Write, run, and deploy client library applications
-   Evaluate community-maintained client libraries for further projects  
    


# Accessing the Kubernetes API

Kubernetes consists of several loosely-coupled components, with the principal idea of managing versioned resources. Kubernetes components can be divided into two sections: the control plane and node components. The control plane consists of the API Server, Controller Managers, and Schedulers. The API Server is the core management component and has the following functionalities:

-   Serves a REST API for the clients outside the cluster and Kubernetes components inside the cluster
-   Creates, deletes, and updates all of the Kubernetes resources, such as pods, deployments, and services
-   Stores the state of the objects in a distributed key/value store  
    

# Kubernetes API Style

The Kubernetes API is a RESTful service that requires all clients to create, read, update, and delete resources using HTTP requests, such as GET, PUT, POST, and DELETE. Client applications such as kubectl or client libraries in various programming languages implement the API response and request types. For communication, the Kubernetes API accepts and returns JSON data, just like most of the RESTful services that are available.  

**Representational State Transfer** (**REST**) is an architectural style for web applications so that they can use HTTP requests. As a convention, GET is used for reading resources, POST is used for creating resources, PUT is used for updating resources, and DELETE is used for deleting resources.  

Servers that apply the RESTful API are expected to have clients without any knowledge of server structure. Likewise, the server should provide all related information for the client so that it can operate and interact with itself.

**JavaScript Object Notation** (**JSON**) is a popular and lightweight data exchange format. JSON is suitable for machine parsing and generation, and is human-readable and expressive. Although written in JavaScript, JSON is supported by multiple languages and is a crucial data type of modern asynchronous browser/server communication.

In the following section, the Kubernetes API response style will be explored by calling an API server REST endpoint.  

# Getting a JSON Response from the Kubernetes API

This section shows how to retrieve raw data from the Kubernetes API by using kubectl and analyze the data as a JSON object for the parts of the resource.

Let's begin by implementing the following steps:  

1.  Get the raw data with the following command:

```markup
kubectl get --raw /api/v1/namespaces/kube-system
```

2.  As a result, you will see a JSON response. Let's get the same command and format the output:

```markup
kubectl get --raw /api/v1/namespaces/kube-system | python -m json.tool
```

If Python is not locally installed, any online JSON formatter can be used by copying and pasting the output.

3.  The JSON response shows the structure of a Kubernetes API resource:

```markup
{"apiVersion": "v1","kind": "Namespace","metadata": {    "creationTimestamp": "2018-04-15T10:21:34Z",    "name": "kube-system",    "resourceVersion": "81",    "selfLink": "/api/v1/namespaces/kube-system",    "uid": "c5db1188-4096-11e8-903d-0800273b4d24"},
```

Kubernetes API resources have "apiVersion" since all resources are versioned in the system. "kind" shows the type of the resource and "metadata" has all of the meta information, such as the creation timestamp, labels, or annotations. "spec" is the part where all properties of the resource are listed. Finally, most of the resources have a "status" section to show their state, errors, or messages (if any).  

# Accessing the Kubernetes API

The Kubernetes API server is secure, requiring all incoming connections to be authenticated. There are two common ways of connecting and securely communicating with the Kubernetes API server. The first one is by using the reverse proxy functionality of kubectl and the second one is by using the API server credentials. These approaches can be summarized as follows:  

-   The reverse proxy Kubernetes API with kubectl:
    -   The Kubectl proxy command starts a proxy server between the localhost and the Kubernetes API server.
    -   All incoming requests are forwarded to the remote Kubernetes API server port.
    -   The API server identity is verified by using self-signed certificates so that no **Man-in-the-Middle** (**MITM**) attacks are is possible.
    -   kubectl handles authentication to the API server. This is a recommended approach in the official Kubernetes documentation.
    -   Further development is ongoing; client-side load balancing and failover features could be provided in the future.
-   Provides the API server address and credentials directly:
    -   The API server address and credentials are available within and outside the cluster and they can be provided as parameters.
    -   This is an alternative approach and should be used as a last resort if the client application cannot work with a reversed proxy.
    -   In order to protect from MITM attacks, certificates should be imported to the clients, for example, through browsers.

In the following activity, connecting to the Kubernetes API by using the kubectl proxy is carried out to create a new Kubernetes namespace. With this method, kubectl securely connects to the API server with its own credentials and creates a proxy for the applications on the local system.

# Connecting to the Kubernetes API and Creating Namespaces

In this section, you are assigned the job of creating namespaces for tests by using the Kubernetes API. Tests are running outside the cluster and communicate with the Kubernetes API. In order to run tests in their own namespaces, you need to create a namespace. With the successful completion of this task, a new namespace will be created within Kubernetes by sending JSON data. Let's ensure to follow these steps before starting with our example:

-   Create a proxy with kubectl and make it available to all applications on the local system
-   Use JSON and cURL to communicate with the Kubernetes API
-   Gather the JSON structure of the namespace resource by querying kubesystem and use it as a template  
    

Let's begin by implementing the following steps:

1.  Start the reverse proxy with the following command:

```markup
kubectl proxy --port=8080
```

2.  In another terminal, create an HTTP request to the forwarded port:

```markup
curl http://localhost:8080/api/v1/namespaces/kube-system
```

The response is expected to be a JSON structure similar to the following:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/7abbc564-ca11-4aee-b64d-853f2c238915.png)

3.  Using the response of _Step 2_ as a template, create a simple namespace JSON object:

```markup
{"apiVersion":"v1","kind":"Namespace","metadata":{"name":"packt-client"}}
```

4.  Create the new namespace by using curl with the payload data from _Step 3_:

```markup
curl -X POST http://localhost:8080/api/v1/namespaces/ \--header "Content-Type: application/json" \--data \'{"apiVersion":"v1","kind":"Namespace","metadata":{"name":"packt-client"}}'
```

As a result of this command, the newly created namespace data will be received:

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/0f9024d6-4866-4623-ad2c-9ca54246b42c.png)

5.  You can run the following command for cleanup:

```markup
kubectl delete namespace packt-client
```

# Accessing the Kubernetes API inside a Cluster

Accessing the Kubernetes API outside the cluster is mostly done for operational bases where human interaction is required. In addition to outside communication, accessing the Kubernetes API inside the cluster to make requests to the API server is also possible. It enables writing applications and running them inside the cluster, which could convert operational knowledge into applications.

For all of the pods in the cluster, Kubernetes injects service accounts – they are the recommended way of authenticating to the Kubernetes API server. For each pod, the following information and credentials related to service accounts are mounted  
by default:  

-   **Service account and token**: /var/run/secrets/kubernetes.io/serviceaccount/token
-   **Certificate bundle**: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
-   **Namespace**: /var/run/secrets/kubernetes.io/serviceaccount/namespace

Using this information within the cluster forms a secure way of connecting to the Kubernetes API server and making requests. The service account, which is an authentication mechanism in Kubernetes, uses signed tokens to verify requests. They are created and managed by the Kubernetes API server. For each pod running in Kubernetes, service account tokens are mounted, and they enable pods to communicate with the Kubernetes API server. Further information is available in the official documentation: [https://kubernetes.io/docs/admin/authentication](https://kubernetes.io/docs/admin/authentication).  

# To Connect to the Kubernetes API inside a Cluster

In this section, we'll create a simple application to query the Kubernetes API and get the details of the kube-system namespace. However, this application should run inside the cluster and work as a Kubernetes native application. We'll query the Kubernetes API within a cluster with the injected environment variables and certificates in the pods.

Let's begin by implementing the following steps:  

1.  Start a cURL instance inside the cluster and wait until it is up and running:  
    

```markup
kubectl run curl --image=tutum/curl --rm -it
```

2.  Inside the pod, check the security credentials:

```markup
ls /var/run/secrets/kubernetes.io/serviceaccount/
```

You'll get the following output:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/33a4ec4e-4602-47db-84d5-277b215b3718.png)

3.  Check that the Kubernetes API server has the related environment variables:

```markup
env | grep KUBE 
```

You'll get the following output:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/dccd7f87-ed37-4ef0-8403-304c6d921ff0.png)

4.  Combine all of the credentials and address information together with the following commands:

```markup
APISERVER=https://$KUBERNETES_SERVICE_HOST:$KUBERNETES_SERVICE_PORTTOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)CACERT=/var/run/secrets/kubernetes.io/serviceaccount/ca.crtNAMESPACE=$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace)
```

5.  With the collected environment variables from _Step 4_, create and send an HTTP request by using cURL:

```markup
curl --header "Authorization: Bearer $TOKEN" --cacert$CACERT $APISERVER/api/v1/namespaces/kube-system 
```

By using the preceding command, a GET request will be sent to the /api/v1/namespaces/kube-system endpoint. In order to authenticate to the API server, a bearer token is sent as a header, and certificate authority information is provided.

As a result of this command, the requested namespace information will be retrieved from the API server:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/32f0bb5d-baac-4ebd-b8bb-9a3385becd49.png)

The Kubernetes API is the core management service and it is a secure RESTful service that consumes JSON. It requires all of the clients to be authenticated, and both outside and inside cluster connections are possible. In the following section, client libraries for various programming languages are presented that implement the Kubernetes API.  

Bookmark

# Official Client Libraries

Applications that consume the Kubernetes REST API should implement API calls, including request and response types. Considering the rich set of Kubernetes resources that are provided to us, developing and maintaining API implementation becomes complex. Fortunately, Kubernetes has a rich set of official client libraries that are implemented in various programming languages. Client libraries do not only handle requests and responses, but also handle authentication to the API server. Besides, most of the client libraries can discover and connect to the Kubernetes API server if it is running inside the cluster.

In this section, official Go and Python client libraries will be presented. The client repositories, documentation, how to install, and how to create simple applications that are running inside and outside the clusters will be covered.  

# Go Client Library

Go, which is also of en referred to as Golang, is a programming language that was created by Google in 2009. Prominent features of Go include the following:

-   It is statically typed so that the compiler ensures object types and conversions are working
-   It has memory safety with no development concerns
-   It has garbage collection with a minimal overhead
-   The structural typing of objects is based on their composition
-   It has first-citizen concurrency handling with primitives such as go routines and channels

Go is a free, open source programming language that has compilers and environment tools. Go became popular within cloud-native applications because the aforementioned features are well-fitting to the requirements of scalable and reliable applications. Some of the most notable projects that use Go as their primary language are as follows:

-   Docker
-   Kubernetes
-   Terraform
-   OpenShift
-   Consul
-   Bitcoin Lightning Network
-   InfluxDB
-   CockroachDB  
    

# Repository

Kubernetes' Go client, namely client-go, is part of the Kubernetes official project, which is available at [https://github.com/kubernetes/client-go](https://github.com/kubernetes/client-go).

It is the oldest and the most comprehensive client library. Kubernetes resource handlers of the client library are generated with the official source code generators from Kubernetes. In addition, client-go is widely used inside Kubernetes projects,  
such as kubectl, helm, and kops.

# Documentation

The Go client repository consists of the following packages and respective focus areas:

-   kubernetes: Clientset to access the Kubernetes API
-   discovery: Discover APIs supported by the Kubernetes API server
-   dynamic: Dynamic client to perform generic API access
-   transport: Authentication and connection start
-   tools/cache: Helpers for writing controllers

The Go client follows the official documentation style of the Go language and it is available at [https://godoc.org/k8s.io/client-go](https://godoc.org/k8s.io/client-go).

# Installation

In the Go language, its toolset provides the go get command as a standard way of downloading and installing packages with their dependencies. This command downloads the default branch and the latest changes from source control version providers. However, specific versions of the Kubernetes client are designed to work with particular versions of dependencies. Therefore, the standard go get command is not usable. Instead, dependency management solutions proposed for Go should be used to work with client-go reliably.

In other words, the required version of client-go should be decided, and then the dependency manager downloads it with the corresponding dependencies. This concept of handling dependencies is called **vendoring**. Accordingly, dependency managers collect the dependency libraries and put them in the vendor folder.

For a Go application that uses the client-go library, all related libraries and their dependencies should be collected under the vendor folder for reliable and repeatable builds.

The Kubernetes Go client supports multiple dependency management tools, such as dep, godeps, and glide. In addition, the required steps for casual users who do not want to use any dependency management tools are provided in the official documentation of client-go: [https://github.com/kubernetes/client-go/blob/master/INSTALL.md](https://github.com/kubernetes/client-go/blob/master/INSTALL.md).

# Creating Configuration

The Go client library provides the necessary functionalities to connect to the Kubernetes API server. It is easy to create the configuration so that you can communicate outside the cluster and inside the cluster. You can do so with the following code snippets:

```markup
// Create configuration outside the cluster config, err = clientcmd.BuildConfigFromFlags("", kubeconfigPath)// Create configuration inside the cluster config, err = rest.InClusterConfig()
```

# Creating Clientset

Clientset contains the clients for each groupof resources and provides access to them. With its redacted version, as shown in the following code, it can be seen that every group of resources have their clients implemented in the client library:

```markup
type Clientset struct {       ...       appsV1 *appsv1.AppsV1Client       ...       batchV1 *batchv1.BatchV1Client       coreV1 *corev1.CoreV1Client       eventsV1beta1 *eventsv1beta1.EventsV1beta1Client       networkingV1 *networkingv1.NetworkingV1Client
       rbacV1 *rbacv1.RbacV1Client       storageV1beta1 *storagev1beta1.StorageV1beta1Client       storageV1 *storagev1.StorageV1Client }
```

Using the configuration from the previous step, clientset can be created with the following code snippet:

```markup
// Create clientset from configuration clientset, err := kubernetes.NewForConfig(config)
```

# Making API Calls

After creating the configuration and clientset, API calls can finally be carried out. All of the Kubernetes resources can be listed, updated, created, or deleted by using the clients in the provided clientset. Some examples are shown in the following code snippet:

```markup
// Request all pods from all namespacespods, err :=clientset.CoreV1().Pods(v1.NamespaceAll).List(metav1.ListOptions{}) // Get deployment packt from the default namespacedeployments, err := clientset.AppsV1().Deployments(v1.NamespaceDefault).Get("packt", metav1.GetOptions{})// Delete statefulset test from namespace packtclientset.AppsV1().StatefulSets("packt").Delete("test", &amp;metav1.DeleteOptions{}) 
```

Code snippets are provided for the configuration, client creation, and making API calls using the Kubernetes Go client in the previous sections. The complete application code is provided in go/main.go, bringing together all of the snippets at [https://goo.gl/wJBjG5](https://goo.gl/wJBjG5).  

We can note the following points in the main.go file:

-   In the main function that was started at _line 19_, all of the variables are defined, and the command-line arguments are parsed at _line 30_.
-   Configuration is created from kubeconfig, and as a fallback method, it is created by in-cluster methods between _lines 33_ and _42_.
-   Clientset is created at _line 45_.
-   Between _lines 51_ and _65_, an indefinite loop is defined with 10 seconds of sleep at the end of iterations.
-   At every iteration of this loop, pods from all namespaces are requested at _line 53_. The response is printed to the console between _lines 58_ and _62_.

In the following example, an application combining all of the code snippets in the previous sections is built and run. It shows you how to build a Go application and use it outside the cluster. Although the application seems straightforward, the flow and codebase creates a foundation for complex automation requirements.  

# To Use the Kubernetes Go Client outside the Cluster

In this section, we'll learn to build and run a Go application, consuming Kubernetes Go client and connecting the application outside the cluster. Go applications are built by using go toolset commands such as go build. However, this requires the installation of Go locally. In this example, we will use the official Docker image of the Go language without any installation on the local machine:

1.  Create a cross-platform build using the official Docker container by using the following command:

```markup
cd gomake build
```

2.  Start the application using the executable we created in _Step 1_ and the kubeconfig file location:

```markup
./client --kubeconfig=$HOME/.kube/config
```

You will see the following output:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/e58345f5-359c-4a91-a83c-06e7412d75b0.png)

# Activity: Using the Kubernetes Go Client inside the Cluster

**Scenario**

You are assigned the task of deploying a Go application that lists all of the pods in Kubernetes. Besides this, the application will run inside the cluster and receive information about its cluster.

**Aim**

To run an application that consumes the Go client library inside the Kubernetes cluster.

**Prerequisites**

1.  Use the Docker image onuryilmaz/k8s-client-example:go image, which contains the executable from the previous example.
2.  Deploy the application and check the logs to see whether it is working as expected.

**Steps for Completion**

1.  Create a deployment with the Docker image of the example client from the previous example.
2.  Wait until the pod is running.
3.  Get the logs of the deployment pod.

With this command, the logs of the pod are retrieved with a subcommand. In the subcommand, all pods are retrieved with the selector label of run equal to go-client, and the name of the first pod is gathered. Logs should indicate the client itself, in addition to other pods in the cluster:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/8c5a13bf-69dd-4d0d-a354-f7a3d71f74bd.png)

4.  Run the following command for cleanup:

```markup
kubectl delete deployment go-client
```

# Python Client Library

Python is a high-level and general-purpose programming language that was first released in 1990. It is one of the most popular open source programming languages, used in various areas, including machine learning, data processing, web development, and scripting. The essential feature of Python is that the language is interpreted with dynamic type checking. Python owes its popularity to its clear programming style and focus on code readability. In modern cloud-native environments, Python is mostly used for infrastructure and automation. In addition to its popularity and widespread usage, Kubernetes has an official client library that's implemented in Python.

# Repository

The Kubernetes Python client is part of the official client repository and is available at [https://github.com/kubernetes-client/python](https://github.com/kubernetes-client/python).

The Python client is an OpenAPI compliant client, which means that Swagger tools generate resource definitions. The client library is still in progress, and its capabilities should be checked from the repository before using them in production. The Python client, like every other Kubernetes client, attempts to support a set of predefined functionalities, and it is classified as "Silver" according to its coverage.

The OpenAPI is a specification for describing RESTful APIs. Using the OpenAPI specification, it is possible to create an implementation for clients and services, including all of the corresponding operations.  
  
Swagger is the tooling ecosystem for developing APIs, which is defined in OpenAPI. Swagger provides both open source and commercial tools to create applications for the provided specification.

# Installation

There are two ways of installing the client library so that you can create a development environment. The first way is to download the source code and build:

```markup
$ git clone --recursive https://github.com/kubernetes-client/python.git$ cd python$ python setup.py install
```

The second way is to download the package from the Python Package Index by using a package manager such as pip:

```markup
$ pip install kubernetes
```

# Client Usage

In the previous section, a Go application that lists all the pods was developed. The same functionality as the previous application is performed in Python in this section. With the clean code and readability philosophy of Python, the same functionality is handled in around ten lines of code, as follows:

```markup
from kubernetes import client, configimport timeconfig.load_incluster_config()v1 = client.CoreV1Api()while True:         ret = v1.list_pod_for_all_namespaces(watch=False)         print('There are {:d} pods in the cluster:'.format(len(ret.items)))         for i in ret.items:                print('{:s}/{:s}'.format((i.metadata.namespace, i.metadata.name))         time.sleep(10)
```

These are the critical points to mention about the preceding code snippet:

-   In _line 3_, the in-cluster configuration, and in _line 5_, the client for the corev1 API are created.
-   Starting in _line 8_, an infinite loop starts with a sleep of 10 seconds at each iteration.
-   In _line 9_, all pods are requested from the v1 client and the response is parsed and written to the console.  
    

# Packaging

The Python application should run inside a container, like all services running on Kubernetes. Thus, the client library defined in this section is packaged with the following Dockerfile. This container definition enables the application to run its isolated environment with its dependencies:

```markup
FROM python:3RUN pip install kubernetesADD . /client.pyCMD ["python", "./client.py"]
```

  
Please refer to the complete code at: [https://goo.gl/z78SKr](https://goo.gl/z78SKr).

The following are remarks about the preceding code:

-   The container has the basis of Python supporting version 3.
-   The Kubernetes Python client library is installed using pip in _line 3_.
-   The client application is copied into the container in _line 5_ and started in _line 7_.

In the following section, the code snippets presented for Python are utilized to work in a Kubernetes cluster. The complete code is packaged as a Docker container with its dependencies. With this container, the application is deployed to Kubernetes in an isolated way, which follows a microservice architecture.  

# Using the Kubernetes Python Client inside the Cluster

In this section, we'll deploy a Python application that lists all of the pods and consumes the Python client library inside Kubernetes. Besides this, the application will run inside the cluster and gather information about its cluster.

Before starting with the implementation, we need to use the Docker image onuryilmaz/k8s-client-example:python, which was built using the Dockerfile in the last section. We also need to deploy the application as a deployment and check the logs to see whether it is working as expected. Let's begin by implementing the following steps:  

1.  Create a deployment with the Docker image of the example client:  
    

```markup
kubectl run python-client -it --image=onuryilmaz/k8sclient-example:python
```

With this command, a deployment with the name python-client will be created with the Docker image onuryilmaz/k8s-client-example:python in an interactive mode so that logs will be printed to the console.

Logs should indicate the client itself, in addition to other pods in the cluster:  

![](https://static.packt-cdn.com/products/9781789619270/graphics/assets/34faffbf-f9cb-4356-8c29-6c32952de9ef.png)

2.  Run the following command for cleanup:  
    

```markup
 kubectl delete deployment python-client
```

# Other Official Client Libraries

In this section, two official Kubernetes client libraries have been covered:

-   **Go**: This is a statically typed compiler-based language
-   **Python**: This is a dynamically typed and interpreted language

Official client libraries also include some additional programming languages:

-   **Java**: [https://github.com/kubernetes-client/java](https://github.com/kubernetes-client/java)
-   **.NET**: [https://github.com/kubernetes-client/csharp](https://github.com/kubernetes-client/csharp)
-   **JavaScript**: [https://github.com/kubernetes-client/javascript](https://github.com/kubernetes-client/javascript)

For the capabilities and hurdles of these libraries, you should check their corresponding repositories since they are all still in the development phase.  


# Community-Maintained Client Libraries

Kubernetes has an active and collaborative open source community, which has also increased its popularity. There are around 20 community-maintained client libraries that are listed in the Kubernetes documentation, which cover the following languages:

-   Clojure
-   Go
-   Java
-   Lisp
-   Node.js
-   Perl
-   PHP
-   Python
-   Ruby
-   Scala
-   dotNet
-   Elixir

There are some critical points to consider before using a community-maintained client library:  

-   **Aim of the library**: It is crucial to consider the aim of the development team and library. Although it seems not directly related to the software itself, it affects how the client library is developed. For instance, some libraries focus on simplicity and compromise on capability coverage. If the vision of your application and the client library don't match, it would be difficult to maintain the application in the long run.
-   **Version and support**: Official libraries support specific Kubernetes API versions and maintain a compatibility matrix. It is critical to work with the client libraries that work with your Kubernetes cluster, and it is also essential to get support for future Kubernetes versions. A community-maintained client library could be very suitable today but depreciate in six months if not supported.  
    
-   **Community interest**: If the considered client library is open source, its community should be alive and interested in making the library better. It is very common to see some libraries start very well but not be maintained due to a missing community. It is not advised to use a client library with old issues without any comments or pull requests that are not reviewed for a very long time.  
    


# Summary

In this section, Kubernetes API access and client libraries were discussed. Although there are various tools for communicating with Kubernetes, knowing the Kubernetes API itself and the client libraries is crucial for creating game-changing automation and orchestration tasks.  
Firstly, the Kubernetes API style and how to connect using HTTP clients was presented. Following that, the client libraries of Kubernetes were covered, and we focused on two official client libraries. For both Go and Python, how to install, write code, package, and deploy this code into cluster steps was done with demonstrations and activities.  


Finally, community-maintained libraries for different language preferences or custom requirements were shown. With the knowledge and hands-on experience of Kubernetes client libraries, higher levels of automation and extending Kubernetes is possible. In the following section, the best practices covered in the first section and the client libraries included in this section are gathered together to create applications that extend Kubernetes.
