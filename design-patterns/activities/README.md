# Activity Solutions

This section contains the worked-out answers for the activities present in each lesson. Note that in case of descriptive questions, your answers might not match the ones provided in this section completely. As long as the essence of the answers remain the same, you can consider them correct.


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
