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
kubectl delete namespace client
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

