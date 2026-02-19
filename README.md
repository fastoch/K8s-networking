# Resources

- https://www.youtube.com/watch?v=sjaOsHoF6KY (Feb 2026)

# Intro

When you deploy your applications to Kubernetes, most of the time you want them to interact with other applications in the cluster 
(Microservices architecture), as well as expose some of them to the Internet.  

And since the Nginx Ingress controller is retiring in March 2026, it's time to compare existing replacement options.  

# ClusterIP

When you deploy an application in K8s using a Deployment, it creates as many pods as you specify in the spec > replicas section of your manifest.  

Since pods are ephemeral, every time they get replaced, they also get a new IP address.  
Which is why we need to create a service of type ClusterIP, so that our pods are reachable by other applications running within the same cluster.  

A ClusterIP service uses a label selector to get all pods IP addresses.  
We can use the following command to verify that our service label selector is correct and that we have the IP addresses of all pods:  
`kubectl get pods -o wide`  

A ClusterIP also gets a static virtual IP address, as well as a DNS name

1/13
