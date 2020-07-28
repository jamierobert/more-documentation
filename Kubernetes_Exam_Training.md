# Kubernetes Exam Training

## Systemd

Systemd is used a lot in CKA exam so it is useful to know from an admin perspective. As we are managing our own cluster this maybe required at work but not for CKAD exams.This is a quick cheatsheet that highlights useful commands.

##### Viewing System Information

	systemctl list-dependencies : Show a unit’s dependencies
	systemctl list-sockets : List sockets and what activates
	systemctl list-jobs : View active systemd jobs
	systemctl list-unit-files : See unit files and their states
	systemctl list-units : Show if units are loaded/active
	systemctl get – default : List default target (like run level)

##### Working with services

	systemctl stop service : Stop a running service
	systemctl start service : Start a service
	systemctl restart service : Restart a running service
	systemctl reload service : Reload all config files in service
	systemctl status service : See if service is running/enabled
	systemctl enable service : Enable a service to start on boot
	systemctl disable service : Disable service–won’t start at boot
	systemctl show service : Show properties of a service (or other unit)
	systemctl -H host status network : Run any systemctl command remotely

##### Changing System State

	systemctl reboot : Reboot the system (reboot.target)
	systemctl poweroff : Power off the system (poweroff.target)
	systemctl emergency : Put in emergency mode (emergency.target)
	systemctl default : Back to default target (multi-user.target)

##### Viewing log messages

	journalctl : Show all collected log messages
	journalctl -u network.service : See network service messages
	journalctl -f : Follow messages as they appear
	journalctl -k : Show only kernel messages

## Kubernetes

### Kubernetes Basic Commands

The first thing to note is that we can get a list of any object with `get <object_types>` where `<object_types>` is the plural form of the object type. - so pods,nodes,namespaces,services..etc.

And we can get detailed information on any object using:

`kubectl describe <object_type> <object_name>`

`kubectl api-resources` - List all resources

`kubectl get pods -n kube-system`

`-n kube-system` Specify namespace for command to system namespace

`kubectl get pod $pod_name`

`kubectl api-resources -o name`
	
`kubectl get pods -n kube-system`
	
`kubectl get nodes`
	
`kubectl get nodes $node_name`
	
`kubectl get nodes $node_name -o yaml`
	
`kubectl describe node $node_name`

`kubectl edit pod $pod_name` - This is a runtime editor that alows quickly editing pod details - not everythnig can be done like this - then we either have to edit yaml and apply or kill /recreate somehow.

`vi my-pod.yml` - create a pod.yaml

Example .yaml
	
	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-pod
	  labels:
	    app: myapp
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
	    


`kubectl create -f my-pod.yml` - Create pod from .yaml
`apply -f my-pod.yml` - Update pod by applying changes in .yaml
`edit pod my-pod` - Edit pod properties at runtime (Not possible to edit everything)
`delete pod my-pod` - Delete a pod.

We often want to delete all pods in this case we can use: `--all` **This may work with other commands too**

### Kubernetes Objects

Have a number of attributes - two significant attributes are:

- spec - The desired state of the object
- status - The actual state of the object

#### Namespaces:

Not on exam but important to be able to use for exam/work:

NS are a way to keep objects organized:

All objects are assigned to a namespace - if not specified they will use the default namespace:


Can be used to organise:

Apps
Teams
Environments

`kubectl get namespaces`

Should output: 

	NAME          STATUS   AGE
	default       Active   68m
	kube-public   Active   68m
	kube-system   Active   68m

These are all created by Kube.

Namespaces are a bit of a pain - If we do get pods we only get the pods for the default namespace - Otherwise we have to 


##### Namespace Commands

`kubectl create ns my-ns`

To add namespace to .yaml use metadata.namespace tag:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-ns-pod
	  namespace: my-ns
	  labels:
	    app: myapp
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']


The below commands target specific ns (If not specified `kubectl` will only target default):

`kubectl get pods -n my-ns`
`kubectl describe pod my-ns-pod -n my-ns`


#### Basic container config


`command`, `args` and `container port`

How to specify `command` and `args` in .yaml:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-args-pod
	  labels:
	    app: myapp
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['echo']
	    args: ['This is my custom argument']
	  restartPolicy: Never




How to specify a port:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-containerport-pod
	  labels:
	    app: myapp
	spec:
	  containers:
	  - name: myapp-container
	    image: nginx
	    ports:
	    - containerPort: 80

## LAB 1

Deploying an nginx pod listening on port 80

	apiVersion: v1
	kind: Pod
	metadata:
	  name: nginx-pod
	  namespace: web
	spec:
	  containers:
	    - name: nginx-container
	      image: nginx
	      command: ['nginx']
	      args: ['-g', 'daemon off;', '-q']
	      ports:
	        - containerPort: 80


#### Config Maps

Config Maps can be used to provide application configuration:

This is one of the ways to pass Command Line args to a kube app.

.yaml for a config map:


	apiVersion: v1
	kind: ConfigMap
	metadata:
	   name: my-config-map
	data:
	   myKey: myValue
	   anotherKey: anotherValue


Passing data from Config Map as Environmennt Variable:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-configmap-pod
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', "echo $(MY_VAR) && sleep 3600"]
	    env:
	    - name: MY_VAR
	      valueFrom:
	        configMapKeyRef:
	          name: my-config-map
	          key: myKey

Passing Config map as mounted volume:


	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-configmap-volume-pod
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', "echo $(cat /etc/config/myKey) && sleep 3600"]
	    volumeMounts:
	      - name: config-volume
	        mountPath: /etc/config
	  volumes:
	    - name: config-volume
	      configMap:
	        name: my-config-map

#### Commands for Viewing Config

	kubectl logs my-configmap-pod
	
	kubectl exec my-configmap-volume-pod -- ls /etc/config
	
	kubectl exec my-configmap-volume-pod -- cat /etc/config/myKey


#### Security Config

If we need to limit the execution of an individual container to a particular user group we can create a securityContext.

security-context-pod.yaml


	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-securitycontext-pod
	spec:
	  securityContext:
	    runAsUser: 2000
	    fsGroup: 3000
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', "echo Hello Kubernetes! && sleep 3600"]


This relies on UID of 2000 and GID of 3000 existing on the system. These can be created with:

	sudo useradd -u 2000 container-user
	sudo groupadd -g 3000 container-group


### Resource requests and limits

[Docs](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#resource-requests-and-limits-of-pod-and-container)

Limits and requests can be set in the below units:

Memory

- Mi = megabytes

CPU's

- m = millicpus

resources.yaml 

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-resource-pod
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
	    resources:
	      requests:
	        memory: "64Mi"
	        cpu: "250m"
	      limits:
	        memory: "128Mi"
	        cpu: "500m"
	        
`requests` - specifies the ammount of cpu/memory that you expect the app to use.

`limits` - specifies a upper bound for each resource - once this value is breached kubernetes should restart the container.

### Secrets

Storing passwords/API Tokens etc

We create the Secret as a .yaml file as shown below.

secret.yaml

	apiVersion: v1
	kind: Secret
	metadata:
	  name: my-secret
	stringData:
	  myKey: myPassword


This can then be passed to a container as an environment variable with:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-secret-pod
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
	    env:
	    - name: MY_PASSWORD
	      valueFrom:
	        secretKeyRef:
	          name: my-secret
	          key: myKey

### Service Accounts

Don't need to know to much about service accounts - do need to know how to instruct pods to use the service account.



To create a serviceaccount : 

`kubectl create serviceaccount my-serviceaccount`

To use that servcieaccount in a pod:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-serviceaccount-pod
	spec:
	  serviceAccountName: my-serviceaccount
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]

## LAB3




### Multi Container Pods

multi-container-pod.yaml


	apiVersion: v1
	kind: Pod
	metadata:
	  name: multi-container-pod
	spec:
	  containers:
	  - name: nginx
	    image: nginx:1.15.8
	    ports:
	    - containerPort: 80
	  - name: busybox-sidecar
	    image: busybox
	    command: ['sh', '-c', 'while true; do sleep 30; done;']

3 Ways that containers can interact with each other if they are in the same pod:


- 1 - Containers can just use localhost
- 2 - Shared storage volume, one volume mounted to two containers
- 3 - Shared process namespace - Containers can signal each others processes.To use this need to set `sharedProcessNamespace: true` for both services

### micro-Service design patterns


##### Relevant Documentation

[Sidecar](https://kubernetes.io/docs/concepts/cluster-administration/logging/#using-a-sidecar-container-with-the-logging-agent)

[Shared Volume] (https://kubernetes.io/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/)

[Adapter] (https://kubernetes.io/blog/2015/06/the-distributed-system-toolkit-patterns/)


- Sidecar Pod - Enhances main container - ie: pulls in files to main container or anyhting that enhances main container

- Ambasador Pod - Captures and translates network traffic - may transform/capture data - but will forward to main container.

- Adapter Pod - Transform the output from the main container and make it available somehwere. Outgoing logs for example.

## LAB 3

Implementing the Ambassador pattern using HAProxy:

fruit-service-ambassador-config.yml:

	apiVersion: v1
	kind: ConfigMap
	metadata:
	  name: fruit-service-ambassador-config
	data:
	  haproxy.cfg: |-
	    global
	        daemon
	        maxconn 256
	
	    defaults
	        mode http
	        timeout connect 5000ms
	        timeout client 50000ms
	        timeout server 50000ms
	
	    listen http-in
	        bind *:80
	        server server1 127.0.0.1:8775 maxconn 32

fruit-service.yml:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: fruit-service
	spec:
	  containers:
	  - name: legacy-fruit-service
	    image: linuxacademycontent/legacy-fruit-service:1
	  - name: proxy
	    image: haproxy:1.7
	    ports:
	    - containerPort: 80
	    volumeMounts:
	    - name: config-volume
	      mountPath: /usr/local/etc/haproxy
	  volumes:
	  - name: config-volume
	    configMap:
	      name: fruit-service-ambassador-config

busybox.yml (For Testing)

	apiVersion: v1
	kind: Pod
	metadata:
	  name: busybox
	spec:
	  containers:
	  - name: my-app-container
	    image: radial/busyboxplus:curl
	    command: ['sh', '-c', 'while true; do sleep 3600; done']
	    
`kubectl apply -f fruit-service-ambassador-config.yml`
`kubectl apply -f fruit-service.yml`
`kubectl apply -f busybox.yml`
`kubectl exec busybox -- curl $(kubectl get pod fruit-service -o=custom-columns=IP:.status.podIP --no-headers):80`

### Things that caught me out after 1st day

	containers:
	ports:  
	  - containerPort: 80

- Secrets - How to create(I totally blanked)

- ServiceAccount - Need to create account to use it. Also, it is specified at the same level as containers. I specified it in containers - duhhh.
 
- ConfigMap - Specifying the config map went well - Using the config map in Pod - not so well, This was using Volume, but feel it would have gone just as badly using env vars.

# Mid Week Session

## Observability

### Liveness Probes And Readiness Probes

Example of a liveness probe:
 
	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-liveness-pod
	spec:
	  containers:
	  - name: myapp-container
	    image: busybox
	    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
	    livenessProbe:
	      exec:
	        command:
	        - echo
	        - testing
	      initialDelaySeconds: 5
	      periodSeconds: 5

Example of a readiness probe:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-readiness-pod
	spec:
	  containers:
	  - name: myapp-container
	    image: nginx
	    readinessProbe:
	      httpGet:
	        path: /
	        port: 80
	      initialDelaySeconds: 5
	      periodSeconds: 5


### Logs

A sample app that generates some output for us to view:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: counter
	spec:
	  containers:
	  - name: count
	    image: busybox
	    args: [/bin/sh, -c, 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']


Get container logs:

	kubectl logs counter

For a multi-container pod, specify which container to get logs for using the -c flag:

	kubectl logs <pod name> -c <container name>
	
Save container logs to a file:

	kubectl logs counter > counter.log
	

### Installing the Metrics server

	cd ~/
	git clone https://github.com/linuxacademy/metrics-server
	kubectl apply -f ~/metrics-server/deploy/1.8+/
	kubectl get --raw /apis/metrics.k8s.io/
	
	
### Monitoring

[Monitoring](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-usage-monitoring/)

Here are some sample pods that can be used to test kubectl top. They are designed to use approximately 300m and 100m CPU, respectively.

	apiVersion: v1
	kind: Pod
	metadata:
	  name: resource-consumer-big
	spec:
	  containers:
	  - name: resource-consumer
	    image: gcr.io/kubernetes-e2e-test-images/resource-consumer:1.4
	    resources:
	      requests:
	        cpu: 500m
	        memory: 128Mi
	  - name: busybox-sidecar
	    image: radial/busyboxplus:curl
	    command: [/bin/sh, -c, 'until curl localhost:8080/ConsumeCPU -d "millicores=300&durationSec=3600"; do sleep 5; done && sleep 3700']
	apiVersion: v1
	kind: Pod
	metadata:
	  name: resource-consumer-small
	spec:
	  containers:
	  - name: resource-consumer
	    image: gcr.io/kubernetes-e2e-test-images/resource-consumer:1.4
	    resources:
	      requests:
	        cpu: 500m
	        memory: 128Mi
	  - name: busybox-sidecar
	    image: radial/busyboxplus:curl
	    command: [/bin/sh, -c, 'until curl localhost:8080/ConsumeCPU -d "millicores=100&durationSec=3600"; do sleep 5; done && sleep 3700']
    
    
Here are the commands used in the lesson to view resource usage data in the cluster:

```kubectl top pods```
```kubectl top pod resource-consumer-big```
```kubectl top pods -n kube-system```
```kubectl top nodes```

### De bugging

I prepared my cluster before the video by creating a broken pod in the nginx-ns namespace:

	kubectl create namespace nginx-ns
	apiVersion: v1
	kind: Pod
	metadata:
	  name: nginx
	  namespace: nginx-ns
	spec:
	  containers:
	  - name: nginx
	    image: nginx:1.158
	Exploring the cluster to locate the problem
	kubectl get pods

```kubectl get namespace```

```kubectl get pods --all-namespaces```

```kubectl describe pod nginx -n nginx-ns```

Fixing the broken image name

Edit the pod:

```kubectl edit pod nginx -n nginx-ns```

Change the container image to ```nginx:1.15.8```

Exporting a descriptor to edit and re-create the pod.

Export the pod descriptor and save it to a file:

```kubectl get pod nginx -n nginx-ns -o yaml --export > nginx-pod.yml```

Add this liveness probe to the container spec:

	livenessProbe:
	  httpGet:
	    path: /
	    port: 80
	    
Delete the pod and recreate it using the descriptor file. Be sure to specify the namespace:

```kubectl delete pod nginx -n nginx-ns```

```kubectl apply -f nginx-pod.yml -n nginx-ns```

### Lab 4

Liveness probes and Readiness probes

Liveness chacks a contianers health - Readiness makes sure theat the cantainer is ready

Readiness Probe:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: candy-service
	spec:
	  containers:
	  - name: candy-service
	    image: linuxacademycontent/candy-service:2
	    livenessProbe:
	      httpGet:
	        path: /healthz
	        port: 8081 

Liveness Probe:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: candy-service
	spec:
	  containers:
	  - name: candy-service
	    image: linuxacademycontent/candy-service:2
	    livenessProbe:
	      httpGet:
	        path: /healthz
	        port: 8081
	    readinessProbe:
	      httpGet:
	        path: /
	        port: 80
### Lab 5

This used get and top :

```kubectl get pods --all-namespaces```


```kubectl top pod -n <namespace>```

To get pods summary:

```kubectl get pod <pod name> -n <namespace> -o json > out.json```

To get logs:

```kubectl logs <pod name> -n <namespace> > pod.logs```


To get a containers spec from running:

```kubectl get pod <pod name> -n <namespace> -o yaml --export > broken-pod.yml```


## Pod Design

### Labels Selectors and Annotations

Kubernetes labels provide a way to attach custom, identifying information to your objects. Selectors can then be used to filter objects using label data as criteria. Annotations, on the other hand, offer a more freeform way to attach useful but non-identifying metadata. In this lesson, we will discuss labels, selectors, and annotations. We will also demonstrate how to use them in a cluster.

Both labels and annotations are defined within the metadata tag:


Prod labels pod:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-production-label-pod
	  labels:
	    app: my-app
	    environment: production
	spec:
	  containers:
	  - name: nginx
	    image: nginx

Dev labels pod:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-development-label-pod
	  labels:
	    app: my-app
	    environment: development
	spec:
	  containers:
	  - name: nginx
	    image: nginx


[Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)


	kubectl get pods -l app=my-app
	
	kubectl get pods -l environment=production
	
	kubectl get pods -l environment=development
	
	kubectl get pods -l environment!=production
	
	kubectl get pods -l 'environment in (development,production)'
	
	kubectl get pods -l app=my-app,environment=production

Pod with annotations:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: my-annotation-pod
	  annotations:
	    owner: terry@linuxacademy.com
	    git-commit: bdab0c6
	spec:
	  containers:
	  - name: nginx
	    image: nginx


[Annotoations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/)



### Deployments

Deployments provide a variety of features to help you automatically manage groups of replica pods. In this lesson, we will discuss what deployments are. We will also create a simple deployment and go through the process of scaling the deployment up and down by changing the number of desired replicas.


[Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)


Example of a deployment that runs three replicas of Nginx:

	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: nginx-deployment
	spec:
	  replicas: 3
	  selector:
	    matchLabels:
	      app: nginx
	  template:
	    metadata:
	      labels:
	        app: nginx
	    spec:
	      containers:
	      - name: nginx
	        image: nginx:1.7.9
	        ports:
	        - containerPort: 80

Notice the deep nesting of the container and the spec in spec scenario..


	kubectl get deployments
	
	kubectl get deployment <deployment name>
	
	kubectl describe deployment <deployment name>
	
	kubectl edit deployment <deployment name>
	
	kubectl delete deployment <deployment name>

Now we can query the deployment.

### Rolling updates and Rollbacks

One powerful feature of Kubernetes deployments is the ability to perform rolling updates and rollbacks. These allow you to push out new versions without incurring downtime, and they allow you to quickly return to a previous state in order to recover from problems that may arise when deploying changes. In this lesson, we will discuss rolling updates and rollback, and we will demonstrate the process of performing them on a deployment in the cluster.




[Updating a deployment](https://v1-12.docs.kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment)

[Rolling back a deployment](https://v1-12.docs.kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment)

Here is a sample deployment you can use to practice rolling updates and rollbacks.

	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: rolling-deployment
	spec:
	  replicas: 3
	  selector:
	    matchLabels:
	      app: nginx
	  template:
	    metadata:
	      labels:
	        app: nginx
	    spec:
	      containers:
	      - name: nginx
	        image: nginx:1.7.1
	        ports:
	        - containerPort: 80


Perform a rolling update.


```kubectl set image deployment/rolling-deployment nginx=nginx:1.7.9 --record```

The --record command keeps track of the change that were making so we can do a rollback in the future.

Explore the rollout history of the deployment.

The first command gives us info on all roll outs

	kubectl rollout history deployment/rolling-deployment
	
The second command allows us to get more info on a particaulr deployment
	
	kubectl rollout history deployment/rolling-deployment --revision=2

You can roll back to the previous revision like so.

```kubectl rollout undo deployment/rolling-deployment```

```kubectl rollout undo deployment/rolling-deployment --to-revision=1```

You can also control how rolling updates are performed by setting maxSurge and maxUnavailable in the deployment spec:

	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: rolling-deployment
	spec:
	  strategy:
	    rollingUpdate:
	      maxSurge: 3
	      maxUnavailable: 2
	  replicas: 3
	  selector:
	    matchLabels:
	      app: nginx
	  template:
	    metadata:
	      labels:
	        app: nginx
	    spec:
	      containers:
	      - name: nginx
	        image: nginx:1.7.1
	        ports:
	        - containerPort: 80

### Jobs and Cron Jobs


Kubernetes provides the ability to easily run container workloads in a distributed cluster, but not all workloads need to run constantly(This is what we use pods for). With jobs, we can run container workloads until they complete, then shut down the container. CronJobs allow us to do the same, but re-run the workload regularly according to a schedule. In this lesson, we will discuss Jobs and CronJobs and explore how to create and manage them.

[](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)

[](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

[](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/)



##### A Job
	apiVersion: batch/v1
	kind: Job
	metadata:
	  name: pi
	spec:
	  template:
	    spec:
	      containers:
	      - name: pi
	        image: perl
	        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
	      restartPolicy: Never
	  backoffLimit: 4



```kubectl get jobs```

##### Cron Jobs

	apiVersion: batch/v1beta1
	kind: CronJob
	metadata:
	  name: hello
	spec:
	  schedule: "*/1 * * * *"
	  jobTemplate:
	    spec:
	      template:
	        spec:
	          containers:
	          - name: hello
	            image: busybox
	            args:
	            - /bin/sh
	            - -c
	            - date; echo Hello from the Kubernetes cluster
	          restartPolicy: OnFailure


```kubectl get cronjobs```


##### Services


Deployments make it easy to create a set of replica pods that can be dynamically scaled, updated, and replaced. However, providing network access to those pods for other components is difficult. Services provide a layer of abstraction that solves this problem. Clients can simply access the service, which dynamically proxies traffic to the current set of replicas. In this lesson, we will discuss services and demonstrate how to create one that exposes a deployment's replica pods.

[](https://kubernetes.io/docs/concepts/services-networking/service/)
[](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/)


There are 4 types of service:

- ClusterIP - The service is exposed within a cluster using an internal IP address. The service is also available using the cluster DNS.

- NodePort - THe service is exposed externally via a port which listens on each node in the cluster

- LoadBalancer - This only works if your cluster is set up to work with a cloud provider. The service is exposed through a load balancer that is created on the cloud platform.

- ExternalName - This maps the serice to an external address. It is used to allow resources within a cluster to access things outside the cluster through a service. This only sets up a DNS mapping. It does not proxy traffic.


##### Network Policies

From a security perspective, it is often a good idea to place network-level restrictions on any communication between different parts of your infrastructure. NetworkPolicies allow you to restrict and control the network traffic going to and from your pods. In this lesson, we will discuss NetworkPolicies and demonstrate how to create a simple policy to restrict access to a pod.


[Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

In order to use NetworkPolicies in the cluster, we need to have a network plugin that supports them. We can accomplish this alongside an existing flannel setup using canal:

	wget -O canal.yaml https://docs.projectcalico.org/v3.5/getting-started/kubernetes/installation/hosted/canal/canal.yaml
	
	kubectl apply -f canal.yaml
	
Create a sample nginx pod:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: network-policy-secure-pod
	  labels:
	    app: secure-app
	spec:
	  containers:
	  - name: nginx
	    image: nginx
	    ports:
	    - containerPort: 80	


Create a client pod which can be used to test network access to the Nginx pod:


	apiVersion: v1
	kind: Pod
	metadata:
	  name: network-policy-client-pod
	spec:
	  containers:
	  - name: busybox
	    image: radial/busyboxplus:curl
	    command: ["/bin/sh", "-c", "while true; do sleep 3600; done"]

Use this command to get the cluster IP address of the Nginx pod:    


	kubectl get pod network-policy-secure-pod -o wide
	
Use the secure pod's IP address to test network access from the client pod to the secure Nginx pod:


	kubectl exec network-policy-client-pod -- curl <secure pod cluster ip address>

Create a network policy that restricts all access to the secure pod, except to and from pods which bear the ```allow-access: "true"``` label:


	apiVersion: networking.k8s.io/v1
	kind: NetworkPolicy
	metadata:
	  name: my-network-policy
	spec:
	  podSelector:
	    matchLabels:
	      app: secure-app
	  policyTypes:
	  - Ingress
	  - Egress
	  ingress:
	  - from:
	    - podSelector:
	        matchLabels:
	          allow-access: "true"
	    ports:
	    - protocol: TCP
	      port: 80
	  egress:
	  - to:
	    - podSelector:
	        matchLabels:
	          allow-access: "true"
	    ports:
	    - protocol: TCP
	      port: 80


Get information about NetworkPolicies in the cluster:

	kubectl get networkpolicies
	kubectl describe networkpolicy my-network-policy
	


To Provide the `web-gateway` Pod with Network Access to the Pods Associated with the `inventory-svc` Service

First, get a list of existing NetworkPolicies:

```kubectl get networkpolicy```

Examine inventory-policy more closely:

```kubectl describe networkpolicy inventory-policy```

This yields an output of:

	Name:         customer-data-policy
	Namespace:    default
	Created on:   2019-05-05 17:50:37 +0000 UTC
	Labels:       <none>
	Annotations:  kubectl.kubernetes.io/last-applied-configuration:
	                {"apiVersion":"networking.k8s.io/v1","kind":"NetworkPolicy","metadata":{"annotations":{},"name":"customer-data-policy","namespace":"defaul...
	Spec:
	  PodSelector:     app=customer-data
	  Allowing ingress traffic:
	    To Port: 80/TCP
	    From:
	      PodSelector: customer-data-access=true
	  Allowing egress traffic:
	    To Port: 80/TCP
	    To:
	      PodSelector: customer-data-access=true
	  Policy Types: Ingress, Egress

Note that the policy selects pods with the label ```app: inventory```, and provides incoming and outgoing network access to all pods with the label ```inventory-access: true```.

Modify the web-gateway pod with ```kubectl edit pod web-gateway```.

Add the ```inventory-access: "true"``` label to the pod under metdadata.labels.

	metdadata:
	  labels:
	    inventory-access: "true"

Test access to the ```inventory-svc``` like so:

```kubectl exec web-gateway -- curl -m 3 inventory-svc```


## Storage

##### Volumes

how to add a volume:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: volume-pod
	spec:
	  containers:
	  - image: busybox
	    name: busybox
	    command: ["/bin/sh", "-c", "while true; do sleep 3600; done"]
	    volumeMounts:
	    - mountPath: /tmp/storage
	      name: my-volume
	  volumes:
	  - name: my-volume
	    emptyDir: {}


emptyDir puts an empty directory on the node that the container is running on - This means if the container is moved to another node then the volume will nto be accesible but does extend beyond the life of the container.

##### Persitent Volumes and Persistent Volume Claims

Persistent Volume:

	kind: PersistentVolume
	apiVersion: v1
	metadata:
	  name: my-pv
	spec:
	  storageClassName: local-storage
	  capacity:
	    storage: 1Gi
	  accessModes:
	    - ReadWriteOnce
	  hostPath:
	    path: "/mnt/data"

PersistentVolumeClaim:

	apiVersion: v1
	kind: PersistentVolumeClaim
	metadata:
	  name: my-pvc
	spec:
	  storageClassName: local-storage
	  accessModes:
	    - ReadWriteOnce
	  resources:
	    requests:
	      storage: 512Mi
	      
kubectl get pv
kubectl get pvc

Pod to consume from PVC

	kind: Pod
	apiVersion: v1
	metadata:
	  name: my-pvc-pod
	spec:
	  containers:
	  - name: busybox
	    image: busybox
	    command: ["/bin/sh", "-c", "while true; do sleep 3600; done"]
	    volumeMounts:
	    - mountPath: "/mnt/storage"
	      name: my-storage
	  volumes:
	  - name: my-storage
	    persistentVolumeClaim:
	      claimName: my-pvc
	      
	      
### LAB 

1.Create a PersistentVolume:

- The PersistentVolume should be named mysql-pv.
- The volume needs a capacity of 1Gi.
- Use a storageClassName of localdisk.
- Use the accessMode ReadWriteOnce.
- Store the data locally on the node using a hostPath volume at the location /mnt/data.

Answer 1

	kind: PersistentVolume
	apiVersion: v1
	metadata:
	  name: mysql-pv
	spec:
	  storageClassName: localdisk
	  capacity:
	    storage: 1Gi
	  accessModes:
	    - ReadWriteOnce
	  hostPath:
	    path: "/mnt/data"

```kubectl apply -f mysql-pv.yml```


2. Create PersistentVolumeClaim:


- The PersistentVolumeClaim should be named mysql-pv-claim.
- Set a resource request on the claim for 500Mi of storage.
- Use the same storageClassName and accessModes as the PersistentVolume so that this claim can bind to the PersistentVolume.

Answer 2:

		apiVersion: v1
		kind: PersistentVolumeClaim
		metadata:
		  name: mysql-pv-claim
		spec:
		  storageClassName: localdisk
		  accessModes:
		    - ReadWriteOnce
		  resources:
		    requests:
		      storage: 500Mi
	      
	      

3. Create a MySQL Pod configured to use the PersistentVolumeClaim:

- The Pod should be named mysql-pod.
- Use the image mysql:5.6.
- Expose the containerPort 3306.
- Set an environment variable called MYSQL_ROOT_PASSWORD with the value password.
- Add the PersistentVolumeClaim as a volume and mount it to the container at the path /var/lib/mysql.

Answer 3:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: mysql-pod
	spec:
	  containers:
	  - name: mysql
	    image: mysql:5.6
	    ports:
	    - containerPort: 3306
	    env:
	    - name: MYSQL_ROOT_PASSWORD
	      value: password
	    volumeMounts:
	    - name: mysql-storage
	      mountPath: /var/lib/mysql
	  volumes:
	  - name: mysql-storage
	    persistentVolumeClaim:
	      claimName: mysql-pv-claim



Check the status of the pod with ```kubectl get pod mysql-pod```. After a few moments it should be in the RUNNING status.

You can also verify that the pod is interacting with the filesystem at /mnt/data on the node. Log in to the node from the Kube master like this, using the same as the password for the kube master:

```ssh cloud_user@10.0.1.102```

Check the contents of the PersistentVolume directory:

```ls /mnt/data```

You should see files and directories there related to MySQL. These were created by the pod, meaning that your PersistentVolume and PersistentVolumeClaim are working!


### Exam tips
[Tips] (https://medium.com/@iizotov/exam-notes-ckad-c1c4f9fb9e73)

Two hours and about 20 hands-on problems (6 minutes per task!)

in-browser SSH session. You’re welcome to write yaml, json, run kubectl, make direct call APIs via curl, anything, as long as it solves the problem.

You will be given several pre-built Kubernetes environments (I believe I had around five), some environments will be empty for you to deploy to, some — packed with stuff for you to modify. Make sure to pay attention to namespaces — each question will require you to use a specific one.

- Tip #4. Skim through all tasks first. You’re not expected to know everything, be okay with reordering and skipping, each task is independent of others and has a different score. It makes sense to spend a few minutes to skim through all of them and choose the approach that works for you. My mistake was to do the whole thing sequentially, I ended up with a couple of well-paying yet straightforward tasks towards the very end with only a minute left on the clock

- Tip #5. Bookmarking. Bookmark the URIs to save time
- Tip #6. Say no to Google. Get used to finding stuff using built-in search, you won’t be allowed to use Google

- Tip #7. Study samples. Study and deploy samples in the docs and on GitHub, there’s a lot in there you will reuse during the exam


At the time of writing, CNCF uses Gate One to deliver the SSH terminal for CKAD and CKA exams — you’re more than welcome to give it a try beforehand. Probably one thing worth noting is that you’ll be required to use Shift+Insert to paste, Ctrl+Insert to copy and Ctrl+Del to cut. At the time of writing, Gate One’s demo access was broken, you can try Azure Cloud Shell for a similar-ish experience.

The exam environment comes with a handy notepad that I used heavily throughout the exam — it doesn’t mess up yaml indentations and overall works flawlessly. In hindsight, I should not have cleared it with every new question and kept appending yaml — there was an opportunity to partially reuse solutions across questions.

- Yaml-first. When deploying new resources, I would through together a yaml config in the in-browser notepad first, starting either from scratch or reusing samples from Kubernetes docs and then copy and paste it into vim

- No in-place modifications. When modifying existing resources, I would avoid making in-place edits via kubectl edit..., instead exporting to yaml via kubectl get ... -o yaml --export and making edits in vim or the in-browser notepad

- One question — one yaml. In my home directory I created a yaml per task (01.yaml, 02.yaml, etc), which proved super helpful when I applied a botched deployment and had to delete the whole thing with a single kubectl delete -f … command

- Dry runs. It’s self-explanatory really, make sure to always do a dry run using --dry-run before applying your configuration


Tip #10. Speed up deletions. When deleting resources, use --grace-period=1 or --grace-period=0 -force to speed things up

Tip #11. Know the syllabus, don’t overlearn. Be familiar with the exam syllabus and study the Candidate Handbook

Tip #13. Practice. Get some hands-on exposure before attempting the exam to benefit from ‘muscle memory’ and habits. If you don’t use Kubernetes at work, come up with a personal project and work on it for a while

--------------------

[More Tips](https://matthewpalmer.net/kubernetes-app-developer/articles/ckad-exam-guide.html)

-  Used something like 
```kubectl run mydeployment --image=nginx --port=8080 --replicas=5``` to create a deployment quickly, and then do ```kubectl get deployment mydeployment --export -o yaml``` to get the YAML for that deployment. This helps me avoid remembering all the details of writing a deployment YAML.

The run method will soon be deprecated fro deployments - we can then use:

	kubectl create deployment nginx  --image=nginx:1.7.8  --dry-run -o yaml > deploy.yaml
	vi deploy.yaml
	
change the replicas field from 1 to 2
add this section to the container spec and save the deploy.yaml file


	ports:
	  - containerPort: 80
	kubectl apply -f deploy.yaml

Set the ```KUBE_EDITOR``` environment variable if you can't hack using vi / vim. The first thing I did in the exam was ```export KUBE_EDITOR="nano"``` because I'm not proud and I don't mind hardcore Linux people pouring scorn upon me :-)

------------------

In this video, I will share some tips to help you succeed while taking the CKAD exam. These are all things that I found useful when taking the exam myself!

You can set up kubectl autocomplete like so:

	source <(kubectl completion bash)
	
	echo "source <(kubectl completion bash)" >> ~/.bashrc

[Auto Complete](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-autocomplete)

Switch to root with ```sudo -i``` straight away - Some root is required.

Check the context - There are mutiple clusters

```kubectl config use-context <context>``` - This command is given at the start of the question.

ssh is provided to log into clusters node - Look at why I'd need to do.

EKS deep dive and monitoring k8s with prometheus course both on Linux accademy.

----------------------------

	$ kubectl run nginx --image=nginx   (deployment)
	$ kubectl run nginx --image=nginx --restart=Never   (pod)
	$ kubectl run busybox --image=busybox --restart=OnFailure   (job)
	$ kubectl run busybox --image=busybox --schedule="* * * * *"  --restart=OnFailure (cronJob)