#Kubernetes

Important Concepts

Different types of resources:

#### Nodes:

A node in kubernetes is an abstraction on the hardware that is being used to run pods in the cluster. In our case its the ec2 instances that we can see labeled with EKS cluster name.

So there what were running our pods/containers/deployments on. These haven't really been my area of focus but as have you I went on the AWS day so do have some understanding -

So this is the important bit that I have learnt about nodes - when were having issues that are affecting more than one deployment and its not gitlab or nexus ... or .... then check the kube nodes.

```kubectl describe <node>```

ssh on to node - couldn't do as they were all dead 

```kubectl delete node --force```



####Pods:

- Pods are a model of the pattern of multiple cooperating processes which form a cohesive unit of service. They simplify application deployment and management by providing a higher-level abstraction than the set of their constituent applications. Pods serve as unit of deployment, horizontal scaling, and replication. Colocation (co-scheduling), shared fate (e.g. termination), coordinated replication, resource sharing, and dependency management are handled automatically for containers in a Pod. Ie: all containers in a pod are scheduled to the same node therefore they can communicate as on localhost and can access the same hard disks etc… 

```Kubectl run nginx --image=nginx --port=80 --restart=Never```

####Replica sets:

A ReplicaSet’s purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

####Deployments:

-  Deployment is a group of one or more pods managed by a replica set. When we use a deployment containers are automatically restarted if we kill them.  The stop/starting is done by the replica set but we don’t really interact with the replicaset. 

To run a deployment from the Command line we can do:
        
```Kubectl run nginx --image=nginx --port=80```

So if we do

```Kubectl get po```

We see the pods sat there….

and if we do do 

```kubectl delete po <pod_name>```

Then the containers will come back up and we can see them restart from both the time they have been up,  and if were quick enough we can see them getting killed and re-created. We can perform actions on deployments such as scaling, auto-scaling, rollouts, rollbacks … - This allows us to track versions of deployments and examine differences between the deployments history. I won’t be covering them commands as part of this as we use Helm to do this and as such the commands used are different and executed by the CI pipeline - While we are using kubernetes under the hood we don’t use the kube commands.

```Kubectl run nginx --image=nginx --port=80 --expose```

#### Services

Deployments make it easy to create a set of replica pods that can be dynamically scaled, updated, and replaced. However, providing network access to those pods for other components is difficult. Services provide a layer of abstraction that solves this problem. Clients can simply access the service, which dynamically proxies traffic to the current set of replicas. In this lesson, we will discuss services and demonstrate how to create one that exposes a deployment's replica pods.

There are 4 types of service:

* ClusterIP - The service is exposed within a cluster using an internal IP address. The service is also available using the cluster DNS.

* NodePort - The service is exposed externally via a port which listens on each node in the cluster

* LoadBalancer - This only works if your cluster is set up to work with a cloud provider. The service is exposed through a load balancer that is created on the cloud platform.

* ExternalName - This maps the serice to an external address. It is used to allow resources within a cluster to access things outside the cluster through a service. This only sets up a DNS mapping. It does not proxy traffic.

####Jobs:

```Kubectl run busybox --image=busybox --port=80 --restart=OnFailure -- wget -o- $IP:80```

To see the output of a job look in the logs with:

```kubectl logs jobs/busybox```


####Cron Jobs:

```Kubectl run busybox --image=busybox --port=80 --restart=OnFailure -- wget -o- $IP:80```


####Commands:

	Basic Commands (Beginner):
	
	  create         Create a resource from a file or from stdin.
	  expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
	  run            Run a particular image on the cluster
	  set            Set specific features on objects

	Basic Commands (Intermediate):
	
	  get            Display one or many resources
	  explain        Documentation of resources
	  edit           Edit a resource on the server
	  delete         Delete resources by filenames, stdin, resources and names, or by resources and label selector