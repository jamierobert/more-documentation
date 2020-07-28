# Kubernetes Exam Prep

## Core Concepts - 13%

### API primititves

When you create a Kube object under the hood kubectl is taking the .yaml file converting it to json, then making a kubernetes API request to perform the action requested.

Every object that you create must always have 3 top level declarations:

- ```apiVersion``` - Always ```apps/v1``` for a deployment.
- ```kind``` -  always ```Deployment``` - Always begins with uppercase letter.
- ```metadata``` - Contains name as aminimum, for some objects labels are required, and you can also specify namespaces here.

Most objects covered by the CKAD also have a spec, with the exception of `serviceaccount` and `secret` that I've noticed.

The ```spec``` is unique for each kind of object, and can contain many nested fields specific to the configuration of that object.


#### Pods and controllers

Pods are not usually used alone when deployed to a cluster as they are not fault tolerant - Instead we use a controller to manage the pod.
 
A Controller can create and manage multiple Pods for you, handling replication and rollout and providing self-healing capabilities at cluster scope. For example, if a Node fails, the Controller might automatically replace the Pod by scheduling an identical replacement on a different Node.

In general, Controllers use a Pod Template that you provide to create the Pods for which it is responsible.

3 types of controller are:

- Deployment
- Stateful Set
- Daemon Sets

#### Defining Pods

	apiVersion: v1
	kind: Pod
	metadata:
	  name: any-name # We need to add a pod name if we would like to create a pod from a deployments template.
	  labels:
	    app: nginx
	spec:
	  containers:
	  - image: nginx:1.7.9
	    name: nginx
	    ports:
	    - containerPort: 80

In contrast to a deployments template where all fields can be dynamically updated, when deployed as a pod, we can only change the fields: `spec.containers[*].image`, `spec.initContainers[*].image`, `spec.activeDeadlineSeconds` or `spec.tolerations` using the edit command.

## Pod Design - 20%


### Labels,selectors and annotations.

Together they make up a way of adding metadata to our Kubernetes objects.


####Labels:

Are used to select and find collections of objects and they support a minimal logical synta that includes operators: =,== and !=. for equality and inequality. For controllers that support the set syntax there is an extended set of operators: In, NotIn, Exists, DoesNotExist. Exists and DoesNotExist are only available in Selectors and not when using the kubectl CLI.

Examples of sensible labels:

```
"release" : "stable", "release" : "canary"
"environment" : "dev", "environment" : "qa", "environment" : "production"
"tier" : "frontend", "tier" : "backend", "tier" : "cache"
"partition" : "customerA", "partition" : "customerB"
"track" : "daily", "track" : "weekly"
```

Getting pods using labels:

`kubectl get pods -l environment=production,tier=frontend`

`kubectl get pods -l 'environment in (production, qa)'`

`kubectl get pods -l 'environment in (production),tier in (frontend)'`

`kubectl get pods -l 'partition in (customerA, customerB),environment!=qa'` Can mix and match types and make up more complex statements.

Adding labels:

`k label deploy env=dev`

Viewing labels:

`k get po --show-labels`

Deleting Labels:

`k label deploy nginx env-`

#### Selectors


We have two types of selctor:

For Services and replication controllers we use:

	selector:
	    component: redis
	    
For newer controllers such as Job Deployment Replica Set and Daemon set we have:
	
	
	selector:
	  matchLabels:
	    component: redis
	  matchExpressions:
	    - {key: tier, operator: In, values: [cache]}
	    - {key: environment, operator: NotIn, values: [dev]}

	    
	    
#### Annotations:

Fields managed by a declarative configuration layer. Attaching these fields as annotations distinguishes them from default values set by clients or servers, and from auto-generated fields and fields set by auto-sizing or auto-scaling systems.

- Build, release, or image information like timestamps, release IDs, git branch, PR numbers, image hashes, and registry address.

- Pointers to logging, monitoring, analytics, or audit repositories.

- Client library or tool information that can be used for debugging purposes: for example, name, version, and build information.

- User or tool/system provenance information, such as URLs of related objects from other ecosystem components.

- Lightweight rollout tool metadata: for example, config or checkpoints.

- Phone or pager numbers of persons responsible, or directory entries that specify where that information can be found, such as a team web site.

- Directives from the end-user to the implementations to modify behavior or engage non-standard features.


### Deployments

- ```spec``` - Contains replicas, selector and template.

All of these must be present for the deployment to function correctly:

The main parts are metadata and spec...

#### Metadata

The metadata for a deployment with name and label:

	metadata:
	  name: nginx-deployment # The name of the deployment.
	  labels:
	    app: nginx-deployment # Any labels you wish to give to this deployment. 
	                          # These labels do not need to match spec.selector.matchLabels.
	    
#### Spec

The spec of the deployment:

	spec:
	  replicas: 2
	  selector:
	    matchLabels:
	      app: nginx # This label must == spec.template.metadata.labels
	  template:
	    metadata:
	      labels:
	        app: nginx # This label must == spec.selector.matchLabels
	    spec:
	      containers:
	      - name: nginx-proxy
	        image: nginx:1.7.9
	        ports:
	        - containerPort: 80

The spec of a deployment contains 3 top level declarations:

1. ```replicas:``` = the number of replica containers.

2. ```selector:``` = used with matchLabels to slect which app should be managed by this deployment.

```spec.selector.matchLabels``` - is a crucial part of a deployment as it is used to tell the deployment which pods it should manage.

If ```spec.selector.matchLabels``` or ```spec.template.metadata.labels``` are updated independently with an ```edit``` command then an error will be returned. It is possible to to update the labels in the deployment but only if we change the name of the dpeloyment. This will cause any currently deployed pods to be  orphaned by the deployment, as they will still have the old label. And, will also cause new pods to be deployed with the new label, that will be manged by the updated deployment.

When we get into this situation with orphaned pods it will not be possible to remove the pods without first re-instating the deployment with the correct labels and deleting the deployment. It may also be possible to remove the pods with ```--force --grace-period=0```

3. ```template:``` = The specification of any containers that should be managed as part of this deployment.

A ```template``` is exactly the same as a pod specification only we do not need to include ```apiVersion``` or ```kind``` when specifying a deployment template. However, we do need to specify a pod name when deploying a pod that is not managed by a deployment.This template defines the same pod that was given in x.x.


#### Managing The Deployment Lifecycle

- `k run load-balancer --image=nginx:1.7.9 --record`

- `k set image deploy/load-balancer load-balancer=nginx:1.9.1 --record`

- `k annotate deploy load-balancer kubernetes.io/change-cause="Added NEW_VAR."`

- `k set env load-balancer NEW_VAR=anothervar --record`

- `k set serviceaccount load-balancer my-svc-account`

#### Deployment Statuses

- Progressing - When deployment is creating or scaling rs
- Complete - All replicas are updated. All instances of previous deployments are destroyed.
- Fail To Progress - The reasons for this are:
	- Insufficient Quota
	- Readiness probe failures
	- Image pull errors
	- Insufficient permissions
	- Limit Ranges
	- Application runtime misconfiguration.

`kubectl patch deployment nginx -p '{"spec":{"progressDeadlineSeconds":30}}'`

### Jobs

Usual 4 top level decs

- `spec.parallelism`
- `spec.completions`
- `spec.backoffLimit`
- `spec.activeDeadlineSeconds`


#### Example job

	apiVersion: batch/v1
	kind: Job
	metadata:
	  name: pi-with-timeout
	spec:
	  parallelism: 4
	  completions: 5
	  backoffLimit: 5
	  activeDeadlineSeconds: 100
	  template:
	    spec:
	      containers:
	      - name: pi
	        image: perl
	        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
	      restartPolicy: Never
	      
As ususal 4 top level declerations with usual requirements, only requirement on spec is `spec.template`.


`kubectl create job my-job --image=busybox -- date`

`kubectl run my-job --restart=OnFailure --image=busybox -- date`


#### Parallel Jobs
I think it may be beyond the scope of the exam but defo worth looking at in the futurre

[Job Patterns Advanced Usage](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/#job-patterns)

#### Cron Jobs


`kubectl create cronjob my-job --image=busybox --schedule='* * * * *' -- date`

`kubectl run another-job --restart=OnFailure --image=busybox --schedule='* * * * *' -- date`


	Examples:
	  # Create a job
	  kubectl create job my-job --image=busybox
	
	  # Create a job with command
	  kubectl create job my-job --image=busybox -- date
	
	  # Create a job from a CronJob named "a-cronjob"
	  kubectl create job test-job --from=cronjob/a-cronjob
	  
	  
	  
## Multi-Container Pods - 10%

The primary reason that Pods can have multiple containers is to support helper applications that assist a primary application. Typical examples of helper applications are data pullers, data pushers, and proxies. Helper and primary applications often need to communicate with each other. Typically this is done through a shared filesystem, as shown in this exercise, or through the loopback network interface, localhost. An example of this pattern is a web server along with a helper program that polls a Git repository for new updates.

The below providees a way for Containers to communicate during the life of the Pod. If the Pod is deleted and recreated, any data stored in the shared Volume is lost.




### Sidecar - Enhances the main container.

A synchronizer container of some sort ..... Git,S3... etc.
Sidecar logger.


### Ambassdor - Proxies all network communication.

A very similar pattern except the difference being that **EVERY** interaction with the outside world goes through this container.

### Adapter - Converts application output to some other format.

Converts output of your app to another systems expected format.The example that was given was monitoring.

#### Sharing data between pods

	apiVersion: v1
	kind: Pod
	metadata:
	  name: two-containers
	spec:
	  restartPolicy: Never
	  volumes:
	  - name: shared-data
	    emptyDir: {}
	  containers:
	  - name: nginx-container
	    image: nginx
	    volumeMounts:
	    - name: shared-data
	      mountPath: /usr/share/nginx/html
	  - name: debian-container
	    image: debian
	    volumeMounts:
	    - name: shared-data
	      mountPath: /pod-data
	    command: ["/bin/sh"]
	    args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]

### Sharing process namespace between pods

It's possible for conatainers to share p[rocess namepace, this makes it possibel to kill another containers peocesses from a sidecar container - this makes it easy to debug things.To do thsi we need ot set `shareProcessNamespace: true` in the `pod.spec`

	  spec:
	    shareProcessNamespace: true
	    containers:  
	    - image: nginx
	      name: nginx
	      
	    - image: busybox
	      name: share-proc
	      securityContext:
	        capabilities:
	          add:
	          - SYS_PTRACE
	      stdin: true
	      tty: true

	
`tty` tells the container to pre-allocate it's terminal and `stdin` does the same for stdin. This is required for this to function correctly - not just with `attach` but also with `exec`.


## Configuration - 18%

### Resource Quotas

`kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run -o yaml`

Need to cover this as it tripped me up in the test.

### Config Maps


There are 2 steps to using a config map:

#### Create

- Can be created with ` kubectl create`
- Use `--from-literal=key=val` to specify values to create.
- To specify multiple values from command line

```k create cm config-5 --from-literal=key=val --from-literal=key1=val1 --from-literal=key2=val2```

- To specify multiple values from a file.
    
    1. Use a file with `--from-file=<filename>`.

         ```
         enemies=aliens
         lives=3
         enemies.cheat=true
         ```
         To use a key other than the filename use: `--from-file=<key>=<filename>`.
         
    2. Use env file with `--from-env-file=<filename>`.

    This is the same as from file but the file has syntax rules that ignores comments `#`and whitespace but preserves quotation marks in values.
    
    
It's also possible to generate config maps from code - by defining a `kustomization.yaml`, this is not on the CKAD.

#### Consume

1. As individual environment variables:

Use `spec.containers.env`
	
	env:
	- name: SPECIAL_LEVEL_KEY
	  valueFrom:
	    configMapKeyRef:
	      name: special-config # Name of cm
	      key: special.how # key of value.

This can be done using more than one `cm`'s per `pod`.

2. To consume all of a `cm` as environment variables where the key in the `cm` is the name of the exposed variable.

Use `spec.containers.envFrom`

	envFrom:
	- configMapRef:
	  name: special-config

3. To use the environment variables in `spec.containers.command` using kubernetes variable substitution syntax.

		command: [ "/bin/sh", "-c", "echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]


4. To add config map data to a specific path on a volume. - Don't know if I need to know this for the exam or not.

		spec:
		  containers:
		    - name: test-container
		      image: k8s.gcr.io/busybox
		      command: [ "/bin/sh","-c","cat /etc/config/keys" ] 
		      
		      # Notice the path is a combination of 
		      # containers.volumeMounts.mountPath and 
		      # volumes.configMap.items.path.
		      
		      volumeMounts:
		      - name: config-volume
		        mountPath: /etc/config
		        
		  volumes:
		    - name: config-volume
		      configMap:
		        name: special-config
		        items:
		        - key: SPECIAL_LEVEL
		          path: keys

> Note : Mounted ConfigMaps are updated automatically. When a ConfigMap already being consumed in a volume is updated, projected keys are eventually updated as well. Kubelet is checking whether the mounted ConfigMap is fresh on every periodic sync. However, it is using its local ttl-based cache for getting the current value of the ConfigMap. As a result, the total delay from the moment when the ConfigMap is updated to the moment when new keys are projected to the pod can be as long as kubelet sync period (1 minute by default) + ttl of ConfigMaps cache (1 minute by default) in kubelet.


#### Edit

It is possible to use `kubectl edit` to edit the keys and values within a config map.

#### Restrictions

1. A pod will fail to start if it refences a cm that does not exist.
2. Keys with invalid names will fail to be created when you create vars using envFrom. This will be recorded in the event log `k get events`.
3. cm's can only be referenced by pods in the same namespace.
4. Can't use cm's with [Static Pods](https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/).

### Security Contexts (More Time Required - [Docs](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/))
A security context can be defined using `pod.spec.securityContext` - If we do not specify a SecurityContext then artifacts created by the application will be created as root and the application will be able to access resources that are created/managed by root.


	securityContext:
	  runAsUser: 1000
	  runAsGroup: 3000
	  fsGroup: 2000

When running id as shown below we see gid 3000 if group is not specified and it will remain zero hence being able to access files created by root.

	$ id
	uid=1000 gid=3000 groups=2000

- fsGroup is a special supplemental group that applies to all containers in a Pod. The owner for any volumes in the pod and any files created in that volume will be Group ID 2000, allowing a shared file store hence `fsGroup`. This attribute is only available to a pod level securityContext.

A security context can be set at both the pod and container level using `pod.spec.securityContext` and `pod.spec.containers.securityContext`. Each level has it's own attributes that only make sense in that context, or are only available in that context. For example:

**Common**

- runAsUser - run process with user id
- runAsGroup - run process with group id
- runAsNonRoot - a check is performed to make sure this pod/container is not being executed as root.
- seLinuxOptions - Options for security enhanced Linux

**Only Pod**

- supplementalGroups - groups other than the processes group.
- fsGroup - Pods filestore group id.

**Only Container**

- allowPrivilegeEscalation - **bool**: Controls whether a process can gain more privileges than its parent process.
- capabilities - Allows granular management of the priviliges given to root.
- privileged - **bool**: Run as root.
- procMount -
- readOnlyRootFilesystem - **bool**: defaults to false, Does what it says.

Example yaml:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: security-context-demo-2
	spec:
	  securityContext:
	    runAsUser: 1000
	  containers:
	  - name: sec-ctx-demo-2
	    image: gcr.io/google-samples/node-hello:1.0
	    securityContext:
	      runAsUser: 2000
	      allowPrivilegeEscalation: false
	      capabilities:
	        add: ["NET_ADMIN", "SYS_TIME"]

#### Resources

To define a pods resources using run:

- To specify limits `--limits='cpu=100m,memory=256Mi'` 
- To specify request `--requests='cpu=100m,memory=256Mi'`

Resources can not be updated with the edit command for a pod, but can for a deployment.

They can also be edited with `k set resources deploy/nginx --limits='cpu=800m' --record`

As we can see this has a distinct advantage over other methods.

	REVISION  CHANGE-CAUSE
	1         <none>
	2         <none>
	3         kubectl edit deploy nginx --record=true
	4         kubectl edit deploy nginx --record=true
	5         kubectl set resources deploy/nginx --limits=cpu=800m --record=true

##### How resources dictate QOS
- Guaranteed: When requests == limits for cpu.
- Burstable: When there is a request for cpu or ram, but container does not meet QOS guaranteed.
- Best Effort: When a container does not specify any limits or requests.

### Secrets (Need to cover Limitations, Risks and Best Practices again - [Docs](https://kubernetes.io/docs/concepts/configuration/secret/))

A secret can be used with a pod in two ways: as files in a volume mounted on one or more of its containers, or used by kubelet when pulling images for the pod.

Two steps to using;

##### Create

To create Secrets from a file:

`k create secret generic db-creds --from-file=password.txt --from-file=username.txt`

To create Secrets from a literal:

`k create secret generic db-creds-2 --from-literal=username=user`

##### Consume

A secret can be consumed as a volume and their is a default-token assigned to every pod as a volume.This default token allows the pod to communicate wiith the kube API Server.This default can be turned off (and ypu can manage your own certificates) but the default-token approach is reccomended.

Code from a deployed PVC example pod.....

	  volumes:
	  - name: default-token-v2d4v
	    secret:
	      defaultMode: 420
	      secretName: default-token-v2d4v

Can also consume own secrets this way (This does not work on docker-desktop as fs is read only):

	apiVersion: v1
	kind: Pod
	metadata:
	  name: mypod
	spec:
	  containers:
	  - name: mypod
	    image: redis
	    volumeMounts:
	    - name: foo
	      mountPath: "/etc/foo"
	      readOnly: true
	  volumes:
	  - name: foo
	    secret:
	      secretName: mysecret
	      # OPTIONAL - extend path/provide key.
	      items:
	      - key: username
	        path: my-group/my-username

##### OPTIONAL - What will happen?

- username secret is stored under /etc/foo/my-group/my-username file instead of /etc/foo/username.
- password secret is not projected.

If .spec.volumes[].secret.items is used, only keys specified in items are projected. To consume all keys from the secret, all of them must be listed in the items field. All listed keys must exist in the corresponding secret. Otherwise, the volume is not created.


Consume as an environment varaible:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: secret-env-pod
	spec:
	  containers:
	  - name: mycontainer
	    image: redis
	    env:
	      - name: SECRET_USERNAME
	        valueFrom:
	          secretKeyRef:
	            name: mysecret
	            key: username
	      - name: SECRET_PASSWORD
	        valueFrom:
	          secretKeyRef:
	            name: mysecret
	            key: password
	  restartPolicy: Never

You can edit secrets with `kubectl edit`.

To decode a secret `echo 'MWYyZDFlMmU2N2Rm' | base64 --decode`

### Service Accounts

- A service account provides an identity for processes that run in a pod.

- If a Service Account is not specified then resources are assigned the `default` service account.
- Run `kubectl create sa` to create a svc account.

- The Yaml for a sa:

```
apiVersion: v1
kind: ServiceAccount
metadata:
  creationTimestamp: null
  name: my-account
```

## State Persistence - 8%

### Understand PersistentVolumeClaims for storage 

A bit of background on volumes for development.

#### Volume Mounts:

	apiVersion: v1
	kind: Pod
	metadata:
	  creationTimestamp: null
	  labels:
	    run: redis
	  name: redis
	spec:
	  containers:
	  - image: redis
	    name: redis
	    volumeMounts:
	      - name: my-volume
	        mountPath: /tmp/mnt
	  volumes:
	    - name: my-volume
	      emptyDir: {}

##### emptyDir

An emptyDir volume is first created when a Pod is assigned to a Node, and exists as long as that Pod is running on that node. As the name says, it is initially empty. Containers in the Pod can all read and write the same files in the emptyDir volume, though that volume can be mounted at the same or different paths in each Container. When a Pod is removed from a node for any reason, the data in the emptyDir is deleted forever.

> Note: A Container crashing does NOT remove a Pod from a node, so the data in an emptyDir volume is safe across Container crashes.

Some uses for an emptyDir are:

- scratch space, such as for a disk-based merge sort
- checkpointing a long computation for recovery from crashes
- holding files that a content-manager Container fetches while a webserver Container serves the data.

By default, emptyDir volumes are stored on whatever medium is backing the node - that might be disk or SSD or network storage, depending on your environment. However, you can set the emptyDir.medium field to "Memory" to tell Kubernetes to mount a tmpfs (RAM-backed filesystem) for you instead. While tmpfs is very fast, be aware that unlike disks, tmpfs is cleared on node reboot and any files you write will count against your Container’s memory limit.


#### Persitent Volume Claims: (More Time Required - [Docs](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/))

Mounting to a nodes directory using hostPath which is only suitable for dev and testing.

Admin Tasks:

1. Create/find a directory to mount on the Kube node.

I used:

```/var/log```

as a target directory so there was no need to create files on docker host.

2. Create a hostPath persitent volume:

		apiVersion: v1
		kind: PersistentVolume
		metadata:
		  name: task-pv-volume
		  labels:
		    type: local
		spec:
		  storageClassName: manual
		  capacity:
		    storage: 1Gi
		  accessModes:
		    - ReadWriteOnce
		  hostPath:
		    path: "/var/log"

`spec` for a pv:

4 Required attributes:

- `spec.storageClassName` - A pvc can claim storage that has the same value for `storageClassName`, you can set storage class name `""` or leave it empty it wil then only be claimed by pvc's that are empty or set to `""`.

- `spec.capacity.storage` - The ammount of storage requested by this pv.

- `spec.accessModes` - Expects a list of possible accessModes.

- `spec.hostPath.path` - Pathe to folder/file to be mounted.

The 3 access modes are,

1. `ReadWriteOnce` - read/write by 1 node.
2. `ReadOnlyMany` - read only by many nodes.
3. `ReadWriteMany` - write only by many nodes.

**Important!** A volume can only be mounted using one access mode at a time, even if it supports many. For example, a GCEPersistentDisk can be mounted as ReadWriteOnce by a single node or ReadOnlyMany by many nodes, but not at the same time.


Dev Tasks - This is what I need to know according to the syllabus:

3. Create a persistent volume claim:

		apiVersion: v1
		kind: PersistentVolumeClaim
		metadata:
		  name: task-pv-claim
		spec:
		  storageClassName: manual
		  accessModes:
		    - ReadWriteOnce
		  resources:
		    requests:
		      storage: 50Mi


For a pvc the only required metadata is name.

- `spec.storageClassName` - A pvc can claim storage that has the same value for `storageClassName`, you can set storage class name `""` or leave it empty it wil then only be claimed by pvc's that are empty or set to `""`.

- `spec.capacity.storage` - The ammount of storage requested by this pvc.

- `spec.accessModes` - Expects a list of possible accessModes.

**spec:**

storageClassName - 

4. Create a pod:

		apiVersion: v1
		kind: Pod
		metadata:
		  creationTimestamp: null
		  labels:
		    run: task-pv-pod
		  name: task-pv-pod
		spec:
		  volumes:
		  - name: task-pv-storage
		    persistentVolumeClaim:
		      claimName: task-pv-claim
		  containers:
		  - name: task-pv-pod
		    image: nginx
		    ports:
		    - containerPort: 80
		    volumeMounts:
		      - mountPath: '/tmp'
		        name: task-pv-storage

The pod that consumes a pvc has to be modified in 2 ways,

We need to specify a volume using `volumes.persistentVolumeClaim.claimName` :

	volumes:
	- name: task-pv-storage        # Can be anything.
	  persistentVolumeClaim:
	    claimName: task-pv-claim   # Name of the claim.

And we need to specify a volume mount `containers.volumeMounts`:

	volumeMounts:
	- name: task-pv-storage   # Name of the volume to be mounted.
      mountPath: '/tmp'       # Path on container that volume wile be available at.




## Services & Networking - 13%


### Services

Services are used to expose pods and deployments and provide the abstraction for de-coupling network communication.This  allows us to bring up containers with different IP addresses without any break in communication.

#### Expose Pods
The set of pods targeted by a service is usually determined by a selector on teh service an a corresponding label on the pod/deployment.

	apiVersion: v1
	kind: Service
	metadata:
	  annotations:
	  name: my-service
	spec:
	  ports:
	  - port: 80
	    protocol: TCP
	    targetPort: 80
	  selector:
	    run: nginx


#### Access External Service without selector.
It is also possible to use a service without a selector, this allows communication with resources other than kubernetes pods such as:

- DB backend
- Service to Service comms in different namespaces
- Can be used to migrate parts of the back end into a cluster.

This can be defined in the same way just with out the selector:

	apiVersion: v1
	kind: Service
	metadata:
	  name: my-service
	spec:
	  ports:
	    - protocol: TCP
	      port: 80
	      targetPort: 9376
	      
We then have to manually create the Endpoint object that is usually created automatically when exposing pods with a service.

	apiVersion: v1
	kind: Endpoints
	metadata:
	  name: my-service
	subsets:
	  - addresses:
	      - ip: 192.0.2.42
	    ports:
	      - port: 9376


#### Multi port services
A single service can be used to expose multiple ports, the only difference when exposing multiple ports is that the ports must have a name.

	apiVersion: v1
	kind: Service
	metadata:
	  name: my-service
	spec:
	  selector:
	    app: MyApp
	  ports:
	    - name: http
	      protocol: TCP
	      port: 80
	      targetPort: 9376
	    - name: https
	      protocol: TCP
	      port: 443
	      targetPort: 9377

#### Service Types

- `ClusterIP`: [default] Service only reachable from within the cluster
- `NodePort`: Exposes the service on each nodes IP, A clusterIP service is also created and the NodePort routes through that you can hit this from outside the cluster on `<NodeIP>:<NodePort>`.
- `LoadBalancer`: Expose externally using Cloud Provider load balancer - can do this with Nginx too.
- `ExtenalName`: Maps to an external name using a `CNAME`

You can also use Ingress to expose your Service. Ingress is not a Service type, but it acts as the entry point for your cluster. It lets you consolidate your routing rules into a single resource as it can expose multiple services under the same IP address.


#### Protocols

- TCP - [default]
- UDP - For LoadBalancer relies on provider support.
- HTTP - HTTP/HTTPS reverse proxy using Load Balncer if supported by provider.(Can also use ingreess)

#### Creatiion using kubectl

To create a svc without applying any Yaml.

use `--expose` when using run commands.
use `k expose deploy nginx`.`--port` `--target-port`, `--type` and `--protocol` are all suported
use `k create svc nodeport my-svc --tcp=80:8080` 

`--cluster-ip=''` ClusterIP to be assigned to the service. Leave empty to auto-allocate, or set to `None` to create a headless service.


### Debugging services:

- Check it exists:

`k run bb --image=busybox --restart=Never -it --rm  -- wget -O- exposed-with-run:5555`

- is DNS Working for this svc:

`k run get-svc --image=busybox --restart=Never -it --rm  -- nslookup hostnames`

Add namespace qualification.

`nslookup hostnames.default`

- Does any service exist in DNS:

`nslookup kubernetes.default`

This goes really in depth for admin but prob too deep to remember for the exam.


#### Network policies - TODO: https://kubernetes.io/docs/concepts/services-networking/network-policies/


	apiVersion: networking.k8s.io/v1
	kind: NetworkPolicy
	metadata:
	  name: test-network-policy
	  namespace: default
	spec:
	  podSelector:
	    matchLabels:
	      role: db
	  policyTypes:
	  - Ingress
	  - Egress
	  ingress:
	  - from:
	    - ipBlock:
	        cidr: 172.17.0.0/16
	        except:
	        - 172.17.1.0/24
	    - namespaceSelector:
	        matchLabels:
	          project: myproject
	    - podSelector:
	        matchLabels:
	          role: frontend
	    ports:
	    - protocol: TCP
	      port: 6379
	  egress:
	  - to:
	    - ipBlock:
	        cidr: 10.0.0.0/24
	    ports:
	    - protocol: TCP
	      port: 5978


## Observability - 18%
### Liveness and readiness Probes

Liveness - Is the application up?

	apiVersion: v1
	kind: Pod
	metadata:
	  labels:
	    test: liveness
	  name: liveness-exec
	spec:
	  containers:
	  - name: liveness
	    image: k8s.gcr.io/busybox
	    args:
	    - /bin/sh
	    - -c
	    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
	    livenessProbe:
	      exec:
	        command:
	        - cat
	        - /tmp/healthy
	      initialDelaySeconds: 5
	      periodSeconds: 5
	      
When a liveness probe fails, the scheduler kills the application 


Can also be used with a named port:

	ports:
	- name: liveness-port
	  containerPort: 8080
	  hostPort: 8080
	
	livenessProbe:
	  httpGet:
	    path: /healthz
	    port: liveness-port



#### Configure Probes

Probes have a number of fields that you can use to more precisely control the behavior of liveness and readiness checks:

- `initialDelaySeconds:` Number of seconds after the container has started before liveness or readiness probes are initiated. Defaults to 0 seconds. Minimum value is 0.

- `periodSeconds:` How often (in seconds) to perform the probe. Default to 10 seconds. Minimum value is 1.

- `timeoutSeconds:` Number of seconds after which the probe times out. Defaults to 1 second. Minimum value is 1.

- `successThreshold:` Minimum consecutive successes for the probe to be considered successful after having failed. Defaults to 1. Must be 1 for liveness. Minimum value is 1.

- `failureThreshold:` When a Pod starts and the probe fails, Kubernetes will try failureThreshold times before giving up. Giving up in case of liveness probe means restarting the container. In case of readiness probe the Pod will be marked Unready. Defaults to 3. Minimum value is 1.
HTTP probes have additional fields that can be set on httpGet:

- `host:` Host name to connect to, defaults to the pod IP. You probably want to set “Host” in httpHeaders instead.
scheme: Scheme to use for connecting to the host (HTTP or HTTPS). Defaults to HTTP.

- `path:` Path to access on the HTTP server.
httpHeaders: Custom headers to set in the request. HTTP allows repeated headers.

- `port:` Name or number of the port to access on the container. Number must be in the range 1 to 65535.
   
### Pods:

When using `k describe po` you can `grep` for `State` if pod is not behaving as expected or `Reason` if pod fails to get quick feedback.

When using `k get events` remember events are namespaced and that `--all-namespaces` can be used.

Also can use `k get po -o yaml` to get all info that is availble for this object.

if `State` is `Completed` then often this can mean the pod is not running any foreground processes and runs as a single batch procces that completes. So it runs goes to completed and then sometime to crashLoopBackoff even though it has actually done what you expected it to do. If  it is genuine use case of batch process then should really be using Job.

### Nodes:

Can use `k describe no` and `k get no -o yaml` 

### What do Kube statuses mean:

#### Pending:

Means that the pods can not be scheduled onto the node, this can mean

- Not Enough Resources: Try adding nodes/ram/cpu, adjust resource requests or removing pods/decreasing replica counts.To find out which one of these you should do use top on nodes and containers.
- You are using Host Port: ports can conflict when using host port or then can be not enough room for allocation(I dont know how this is limited,but it say there is somehow a limit in the docs without any explanation) 


#### Waiting

This usually means failure to pull an image as at this point the pod has been scheduled to a node but for whatever reason the node will not accept the pod.

- Is the name correct
- Is the image where you think it is.
- Run a pull image.

#### Pod is running but misbehaving.

Try apply again with `--validate` as this will give an error if you mis-placed or mistyped a command.

`logs`, `events` and `describe`. Remeber `--previous` for logs.

`kubectl get pods --field-selector status.phase=<>`