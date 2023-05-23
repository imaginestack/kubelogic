## Kubernetes Design Patterns

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

All of the code files for the activities in this chapter are provided on GitHub in the Lesson-1 folder at [https://goo.gl/gM8W3p](https://goo.gl/gM8W3p).

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

Bookmark

# section 1: Kubernetes Design Patterns

Following are the activity solutions for this section.

# Activity: Running a Web Server with Synchronization

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

Bookmark

# section 2: Kubernetes Client Libraries

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