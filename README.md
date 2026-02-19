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

# LoadBalancer (LB)

Now, we're ready to promote our application to production, and instead of NodePort, we can create a service of type LoadBalancer.  

In Cloud enviroments like AWS, for every single LoadBalancer service, AWS will create a dedicated **elastic load balancer**.  
With the new load balancer controller and native VPC networking, AWS will create a network load balancer and add every single pod IP address 
in its target group.  

Other clouds have similar setups as well, where we get a single stable DNS record of the load balancer.  
We can use that DNS record and create a CNAME record for it (a type of DNS record that creates an alias for another domain name).  

This way, we don't need to worry about Node IP addresses, and we can use the default HTTP ports 80 or 443, instead of a custom 
NodePort like 30120.  

<img width="898" height="868" alt="image" src="https://github.com/user-attachments/assets/d9918a00-9d62-4448-b703-06e3d391f539" />  

Another benefit is that we can get a TLS certificate from AWS, add it to the loadbalancer to handle encryption, and send plain 
HTTP traffic to our app.  

The biggest issue with this approach is the cost.  
You have to pay for every single loadbalancer you create, and the price is usually structured in 2 ways:
- you pay an hourly rate for each LB even if not using it
- you pay for the usage

If you have many external applications, it can significantly increase your infrastructure cost.  
If you just have a single app that is exposed to clients, keep using a single LB, as it's the simplest and most reliable production setup.

# Ingress

Instead of using a LB for each external application, you can create a single LB and use an ingress controller to route 
traffic to specific microservices in your Kubernetes cluster.  

For a long time, the most popular ingress controller was the one provided by Nginx.  
When you create an ingress resource (a K8s object) and apply it (`kubectl apply`), the ingress controller parses all those configurations 
and converts them to native Nginx config.  

This approach allows you to have a single LB and use **path-based routing**.  
- if a client requests the "user" endpoint, the request is forwarded to the "user" application
- if the path is /catalog, it forwards the request to the catalog microservice

You can also use **domain-based routing**:
- instead of /user, you



7/13
