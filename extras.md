Kubernetes 


To see container stdout when using kubectl apply just look in the logs. 

To see all pod labels

	kubectl get po --show-labels
	
To view pods with specific label:

		k get pods --selector=app=v1
	
Labels can be edited using edit command but can also be added with


	kubectl label po <name> app=v1

Removing Labels:

	kubectl label po nginx1 nginx2 nginx3 app-
	# or
	kubectl label po nginx{1..3} app-
	# or
	kubectl label po -lapp app-
	
	
Selecting a particular node for a pod to run on:


	apiVersion: v1
	kind: Pod
	metadata:
	  name: cuda-test
	spec:
	  containers:
	    - name: cuda-test
	      image: "k8s.gcr.io/cuda-vector-add:v0.1"
	  nodeSelector: # add this
	    accelerator: nvidia-tesla-p100 # the selection label
	    
This is described under:

	kubectl explain po.spec

Along with lots of other useful stuff


Create annotations

	k annotate po nginx1 description='my description'
	
View Annotations

	k describe po nginx{1..3} | grep -i annotations
	
Remove Annotations

	k annotate po nginx{1..3} description-
	

betteer wsay to get pods

	kubectl get po -l run=nginx # if you created deployment by 'run' command
	kubectl get po -l app=nginx # if you created deployment by 'create' command
	
	
Expose a deployment using a service after the deployment has been made:	
	
	kubectl expose deploy foo --port=6262 --target-port=8080
	
Expose deployment using svc at deploy time:
 
	kubectl run nginx --image=nginx --replicas=2 --port=80 --expose