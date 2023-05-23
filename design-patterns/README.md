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

