## YAML Snippets

#### Config Maps

All of the below examples assume that a cm exists call config with key=val using:

```k create cm config --from-literal=key=val```

1. Consume single value as environment variable:

		apiVersion: v1
		kind: Pod
		metadata:
		  name: cm-as-env-var
		spec:
		  containers:
		  - name: nginx
		    image: nginx
		    env:
		    - name: VAR
		      valueFrom:
		        configMapKeyRef:
		          name: config
		          key: key
		     
		     
2. Consume whole config map to environment variables preserving keys as VAR_NAMES:

		apiVersion: v1
		kind: Pod
		metadata:
		  name: cm-to-volume
		spec:
		  containers:
		  - name: nginx
		    image: nginx
		    envFrom:
		    - configMapRef:
		        name: config

3. Consume whole config map and make it a vailable as a directory on a volume at a custom mount path:

		spec:
		  containers:
		    - name: test-container
		      image: k8s.gcr.io/busybox
		      
		      volumeMounts:
		      - name: config-volume
		        mountPath: /etc/config
		        
		  volumes:
		    - name: config-volume
		      configMap:
		        name: special-config
		        # OPTIONAL - extend path/provide key.
		        items:
		        - key: SPECIAL_LEVEL
		          path: keys


### Secrets

All of the examples below assume that a secret has already been created using:

```kubectl create secret generic my-secret --from-literal=username=jamie```

Which will yield:

```
apiVersion: v1
data:
  password: cGFzc3dvcmQ=
kind: Secret
metadata:
  creationTimestamp: null
  name: my-secret
```

1. Consume a secret as an environment variable:

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
		          name: my-secret
		          key: username

To mount a secret in a volume:

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
	  volumes:
	  - name: foo
	    secret:
	      secretName: mysecret
	      # OPTIONAL - extend path/provide key.
	      items:
	      - key: username
	        path: my-group/my-username




You can edit secrets with `kubectl edit`.

To decode a secret `echo 'MWYyZDFlMmU2N2Rm' | base64 --decode`


### Persistent Volume Claims:

The pod that consumes a pvc has to be modified in 2 ways,

Create the volume:

	apiVersion: v1
	kind: PersistentVolume
	metadata:
	  name: mysql-pv-volume
	  labels:
	    type: local
	spec:
	  storageClassName: manual
	  capacity:
	    storage: 20Gi
	  accessModes:
	    - ReadWriteOnce
	  hostPath:
	    path: "/mnt/data"

Specify which volume to claim `volumes.persistentVolumeClaim.claimName` :

	volumes:
	- name: task-pv-storage        # Can be anything.
	  persistentVolumeClaim:
	    claimName: task-pv-claim   # Name of the claim.

And we need to specify a volume mount `containers.volumeMounts`:

	volumeMounts:
	- name: task-pv-storage   # Name of the volume to be mounted.
      mountPath: '/tmp'       # Path on container that volume wile be available at.


`supported values: "ReadOnlyMany", "ReadWriteMany", "ReadWriteOnce"`


### Liveness Probe:

A liveness probe is defined in `spec.containers.livenessProbe` and can be done pover http, tcp or simply by interacting with the container by running a command.

Using a `exec` command:

```
    livenessProbe:
      initialDelaySeconds: 5
      periodSeconds: 5
      exec:
        command:
        - cat
        - /tmp/healthy    
```	      
Over `httpGet` 
   
```
	 livenessProbe:
	   initialDelaySeconds: 3
	   periodSeconds: 3
	   httpGet:
	     path: /healthz
	     port: 8080
	     httpHeaders:
	     - name: Custom-Header
	       value: Awesome
```

Over `tcpSocket`

```
    livenessProbe:
      initialDelaySeconds: 15
      periodSeconds: 20
      tcpSocket:
        port: 8080
```


Readiness works just he same expcept:

```
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```


### Network Policy


	kind: NetworkPolicy
	apiVersion: networking.k8s.io/v1
	metadata:
	  name: access-nginx # pick a name
	spec:
	  podSelector:
	    matchLabels:
	      run: nginx # selector for the pods
	  ingress: # allow ingress traffic
	  - from:
	    - podSelector: # from pods
	        matchLabels: # with this label
	          access: 'true' # 'true' *needs* quotes in YAML, apparently