## Cheat Sheet

###vi


`set number` turn on line numbers
`dd` cut/delete
`5dd` - delete 5 lines ... etc
`p` paste after.
`P` = paste befor


### Time saving commands

- BusyBox wget

`k run bb --restart=Never --image=busybox -it --rm -- wget -O- http://10.1.10.168:80/`

- Kubens without kubens

```k config set-context $(k config get-context) --namespace=exam```

- print only the resource name and nothing else.

```-o name```	

- Get only the pod name - but get only 3rd line.

```k get po -o name | sed -n '3p'```

- This means we can do stuff like:

```k logs $(k get po -o name | sed -n '1p')``` for when we have hashes in names.

- An example of running a pod, setting an enevironment varaible, printing the result and deleting the container:

```kubectl run nginx --restart=Never --image=nginx --env=var1=val1 -it --rm -- env```

- To pipe the result of a --dry-run -o yaml command to create we can use ```-```:

```kubectl run nginx-from-cli --image=nginx --restart=Never --dry-run -o yaml | kubectl create -n default -f -```

It is also possible to use apply instead of create.

- To view all of the events that have happened on a cluster:

```kubectl get events --sort-by=.metadata.creationTimestamp```

- Create a service for a replicated nginx, which serves on port 80 and connects to the containers on port 8000:

```kubectl expose rc nginx --port=80 --target-port=8000```

- Change default editor to nano:

```KUBE_EDITOR="nano" kubectl edit svc/docker-registry```

- To debug a cluster:

```kubectl cluster-info``` - returns host of Master Node, DNS server and Metrics server.

```kubectl cluster-info dump``` - returns all info on current cluster.

- Using a field selector:

```kubectl get pods --field-selector=spec.nodeName=server01```

-  Get all pods where that are not running.
`k get po --field-selector=status.phase!=Running`

- Get the IP of a svc and use it as an arg passed to the contianer.

`k run bb --image=busybox  --restart=Never -it --rm -- wget -O- $(kubectl describe svc/nginx | grep IP: | awk '{print $2;}'):80`

### Debugging goodness

##### Isolating Pods from a ReplicaSet:

You can remove Pods from a ReplicaSet by changing their labels. This technique may be used to remove Pods from service for debugging, data recovery, etc. Pods that are removed in this way will be replaced automatically ( assuming that the number of replicas is not also changed).

##### Editing files in place with ```kubectl edit```:

A Deployment:

- Labels can be added and removed.
- Api Version, Kind and name can not be edited in place.
- Namespace can not be edited.
- The number of replicas.

##### Editing files that have been exported from a running container:

```k get deploy nginx-deployment -o yaml --export >> deployment-ns.yaml```

When using the above on a deployment we lose some of the cluster specific details to the app, this includes the Namespace, Timestamps, UID and Status.
Therefore it is better to drop ```--export``` if you just want to move a deployed app to a different namespace.

### Common reasons for deployments/pods failing.


- When a container in a deployment completes successfully it will be terminated and go into a ```CrashLoopBackoff``` status, this is annoying and does not really make sense in most cases. The root of the problem is that a deployment will ```Always``` try and restart an instance once it completes. To prevent this issue we should deploy short running containers as ```Pods``` or ```Jobs``` rather than deployments. This allows us to specify ```restartPolicy: OnFailure```

```
	apiVersion: v1
	kind: Pod
	metadata:
	  labels:
	    app: ubuntu-app
	  name: ubuntu
	spec:
	  containers:
	   - name: ubuntu
	     image: ubuntu:latest
	     command: ['echo']
	     args: ['Hello WH']
	  restartPolicy: OnFailure
```
If a pod must be deployed as a deployment but does not have any processes running in the foreground we can fix this by using a Hack.

1. Add a sleep command as above.
2. Run ```/bin/bash``` as the last command to hold the container in a running state.



		apiVersion: networking.k8s.io/v1
		kind: NetworkPolicy
		metadata:
		  name: access-nginx
		spec:
		  podSelector:
		    matchLabels:
		      app: nginx
		  ingress:
		  - from:
		    - podSelector:
		        matchLabels:
		          access: "true"
	          
	          
## Further study:

[Managing Resources at a cluster level] (https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/)

[How to mark a node as having an arbitary resource] (https://kubernetes.io/docs/tasks/administer-cluster/extended-resource-node/)


##### Creating an EBS volume
Before you can use an EBS volume with a Pod, you need to create it.

```aws ec2 create-volume --availability-zone=eu-west-1a --size=10 --volume-type=gp2```

Make sure the zone matches the zone you brought up your cluster in. (And also check that the size and EBS volume type are suitable for your use!)

AWS EBS Example configuration
```
apiVersion: v1
kind: Pod
metadata:
  name: test-ebs
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-ebs
      name: test-volume
  volumes:
  - name: test-volume
    # This AWS EBS volume must already exist.
    awsElasticBlockStore:
      volumeID: <volume-id>
      fsType: ext4
```

[Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)

Endpoint Slices
FEATURE STATE: Kubernetes v1.17 beta
Endpoint Slices are an API resource that can provide a more scalable alternative to Endpoints. Although conceptually quite similar to Endpoints, Endpoint Slices allow for distributing network endpoints across multiple resources. By default, an Endpoint Slice is considered “full” once it reaches 100 endpoints, at which point additional Endpoint Slices will be created to store any additional endpoints.

Endpoint Slices provide additional attributes and functionality which is described in detail in Endpoint Slices.
## Unrelated Docker finds

To get a terminal on the docker desktop kubenretes master node 

```screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty```

[Link](https://kubernetes.io/docs/tasks/configure-pod-container/translate-compose-kubernetes/)


[Link](https://kubernetes.io/docs/tasks/debug-application-cluster/audit/)