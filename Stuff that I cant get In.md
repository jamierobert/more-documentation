### Stuff that I cant get In



- Get events by timestamp: `kubectl get events --sort-by=.metadata.creationTimestamp` 
- `groupadd group-name -g 10000` - add a group
- `useradd user-name -g 20000` - add a user
- `getent passwd` - get all users
- `getent group` - get all groups.

#### A persistent Volume
	
	apiVersion: v1
	kind: PersistentVolume	
	metadata:
	  name: pv
	  labels:
	    type: local    
	spec:
	  storageClassName: manual  
	  capacity:
	    storage: 20Gi 
	  accessModes: [ReadWriteOnce]
	  hostPath:
	    path: "/mnt/data"
	    
#### A Persistent Volume claim:

	apiVersion: v1
	kind: PersistentVolumeclaim
	metadata:
	  name: bertie
	spec:
	  storageClassName: manual
	  accessModes: [ReadWriteOnce]
	  resources:
	    requests:
	      storage: '1Gi'
	    limits:
	      storage: '2Gi'
	  selector:
	    matchLabels:
	      app: nginx

#### Network policies

Network policies isolate pods by using a podSelector:

Once a network policy is applied ot a specific label apps witht theat label will cese to accept connections.

There are then two types of policy Ingress and Egress that act as whitelists for each type of traffic.

This is a comprehensive example:

	apiVersion: networking.k8s.io/v1
	kind: NetworkPolicy
	metadata:
	  name: test-network-policy
	  namespace: default
	  
	  
	  
	spec:
	  podSelector:
	    matchLabels:
	      role: db
	      
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

Get event by time order.

```kubectl get events --sort-by=.metadata.creationTimestamp```

Get sudo access in exam env. 

`sudo -i <command>`


#### Service Accounts

Can be used to distribute secrets to all pods in a namespace.

	apiVersion: v1
	kind: ServiceAccount
	metadata:
	  name: default
	  namespace: default
	  
	secrets:
	- name: a-secret
	
	imagePullSecrets:
	- name: myregistrykey

#### Run pods with a generator


