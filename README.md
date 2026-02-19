# Resources

- https://www.youtube.com/watch?v=sjaOsHoF6KY (Feb 2026)

# Intro

When you deploy your applications to Kubernetes, most of the time you want them to interact with other applications in the cluster 
(Microservices architecture), as well as expose some of them to the Internet.  

And since the Nginx Ingress controller is retiring in March 2026, it's time to compare existing replacement options.  

# ClusterIP

When you deploy an application in K8s using a Deployment, it creates as many pods as you specified in the spec > replicas section of your manifest.  

Since pods are ephemeral, every time they get replaced, they also get a new IP address.  
Which is why we need to create a service of type ClusterIP, so that our pods can be reached by other applications running within the same cluster.  

A ClusterIP service uses a **label selector** to get all pods IP addresses.  
We can use the following command to verify that our service label selector is correct and that we have the IP addresses of all pods:  
`kubectl get pods -o wide`  

A ClusterIP also gets a static virtual IP address, as well as a DNS name that can be resolved only within the K8s cluster.  
The DNS name is the same as the service name itself, but only when you're calling it from within the same namespace.  

You think of ClusterIP as an **internal load balancer** with a built-in DNS name that your application can use out-of-the-box 
to talk to other applications inside the same K8s cluster.  

# NodePort

The simplest way to exposer your service outside your Kubernetes cluster is to use a NodePort.  
When we create a service of type NodePort, K8s still creates ClusterIPs so internal traffic still works.  
It will also reserve a specific port on every single node in your cluster.  

Any traffic that hits any node's IP address on that specific port will be forwarded to your service, which then sends it 
to your pods.  

Your actual application might be running on a different node that the one receiving incoming traffic, but if the request goes 
to a node that doesn't have the pods running your app, the traffic will still be forwarded to the correct node.  

A NodePort makes your service/app reachable from outside the cluster using a node IP address and a port, and the same port is open on every node.  

A NodePort service actually sits on top of a ClusterIP service.  
It's basically an entry point that hands traffic of to the ClusterIP.  
It's very simple and great for quick testing or development environments where you don't need a fancy Cloud LoadBalancer.  
It is also low cost since you don't have to pay for an external cloud provider's LoadBalancer service.  

Usually you only use NodePorts in non-production environements or very specific bare metal scenarios where you don't have access to an automated LoadBalancer.  
This can be a nice starting point in your development environment when you prepare and test your applications without having any real clients.  

# LoadBalancer 

Now, we're ready to promote our application to production.  


5/13
