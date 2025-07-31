# KUBERNETES:

K8S is an open-sourced  platform for automating the deployment, scaling and operation of containerized application.
K8S is a native docker orchestrating tool to work with multiple containers and managing multiple nodes.
With K8S, we can achieve auto scaling
			  we can associate a Load Balancer to expose the container services to customers using URLs.

| **Docker-Swarm**  | **K8S** |
| ------------- | ------------- |
| Cluster       |   Cluster  |
| Nodes  |   Nodes  |
| Containers  |   Pods  |
| Service/Application  |   Containers  |
|   |   Service?application  |
	
In K8S, the elemental level objects that K8S have control is PODS.
meaning, K8S don't really work on container level, they don't care/know about containers. All they work on is PODS.
we can treat PODS as containers for analogy.

	
Kubernetes Architecture:
--------------------------------
With a cluster in K8S, we have two kinds of nodes
  1. Master node/ control plane
  2. Worker node

___**Master node:**___   
  Its primary work is to,  
  * manage the worker nodes,   
  * assign them the work that we desire,   
  * keep the record of meta data ( in Json format).  


___**Slave node/Worker node /Minion:**___
  * PODS ( in which containers run) are present in the worker node.  
  * Does the actual work of running containers.  

A single cluster can have Multiple Worker nodes but mostly 1 Master node.  


<img width="1041" height="813" alt="image" src="https://github.com/user-attachments/assets/d56ab9d3-84c2-4d19-a574-246082037850" />


------------------
	
_**Kubectl**_ :  
DevOps Eng. connects to the K8S cluster using the tool. 
Used to write commands to work with K8S.  
Kubectl is the command line tool for Kubernetes

## MASTER Node:

  1. _**API server**_ : 
      The first line of interaction with k8S. (Entry point for all REST commands used to control cluster.)
             All the commands are reached to Kubeapi server.
      
  2. _**Schedular**_ : 
      This component work is to ASSIGN the no. of  pods to the node based on resource availability and other constrains.
      This will communicate with Nodes.

  3. _**ETCD**_ : 
      This component is like Database, to store the clusters data.
            Data is stored in JSON format ( Key : Value ).
      Data ---> Configuration data, Cluset data, state info, meta data.

  4. _**Controller Manager**_ : 
      This component runs various controllers that regulate the state of the cluster.
      Node controllers, Replication controller, and more.

  5. Cloud Controller manage:

## Worker Node:

  1. _**Kubelet**_ : 
      Agent that runs on every worker node.
      Ensures that containers are running in the PODS as defined.
      Worker nodes talk to master node(API server) through Kubelet and manages the lifecycle of containers on its node.

  2. _**PODS**_ : 
      Smallest and simplest K8S objects. 
      POD represents the single instance of running process in cluster.
      contains container ( one or many).

  3. _**Container runtime**_ :
      Software responsible for running containers.	
      Most common runtime is docker, but supports others like CONTAINERD, CRI-O.
    
      Pulls image from reg. , unpacks them, run as containers.
  
  4. _**Kube-Proxy**_ : 
      Is a network proxy that runs on each worker node.
      Maintains the network rules.
      It is responsible for assigning the Ports to the containers.


## Key Network Components:

  1. _**Cluster Ips**_ :  
      An internal Ip address that is assigned to  service within cluster.

  2. _**Node Ports**_ :
      Expose a service on a static port on each nodes Ip.

  3. _**Load balancer**_ :
      Provisions a load balancer in supported cloud env to expose service externally.


## Volumes:  
  Persistent storage to containers with in pods, allowing data to persist across pod restarts.  
  They can be backed by various storage backends, include local storage, cloud storages like AWS EBS etc.

## Name space:  
  Context: Even when having different ENVs like dev, pre-prod, prods, we only use 1 cluster for all of it.  
            maintaining different clusters cost much.  
  In such cases, Name space is the means to differentiate the resources, PODS, container for different ENVs.

	
## Cluster Types:
  1. _**Self managed**_  
  * minikube = single node cluster
  * kubeadm = multi node cluster (manual)
  * kops = multi-node cluster (automation)

  2. _**Cloud managed**_
  * AWS = EKS = ELASTIC KUBERNETES SERVICE
  * AZURE = AKS = AZURE KUBERENETS SERVICE
  * GOOGLE = GKS = GOOGLE KUBERENETS SERVICE



--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Single Node (Minikube) Setup

Requirement  
Launch an EC2 instance with Ubuntu and t2.small having min 10GB volume.

Install minikube and Kubectl

 ``` vi minikube.sh ```
```
sudo apt update -y
sudo apt upgrade -y
sudo apt install curl wget apt-transport-https -y
sudo curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
sudo chmod +x /usr/local/bin/minikube
sudo minikube version
sudo curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl&quot;
sudo curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256&quot;
sudo echo "$(cat kubectl.sha256) kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
sudo minikube start --driver=docker --force
```


KUBECTL:
--------
kubectl is the CLI which is used to interact with a Kubernetes cluster.  

We can create, manage pods, services, deployments, and other resources.  

The configuration of kubectl is in the $HOME/.kube directory.  

`minikube status`  

`kubectl get nodes`  

`kubectl get pods`

# 1. PODS :
It is a smallest unit of deployment in K8's.  
It is a group of containers.  
Pods are ephemeral (short living objects)  
Mostly we can use single container inside a pod but if we required, we can create multiple containers inside a same pod.  
when we create a pod, containers inside pods can share the same network namespace, and can share the same storage volumes.  
While creating pod, we must specify the image, along with any necessary configuration and resource limits.  
K8's cannot communicate with containers, they can communicate with only pods.   

We can create this pod in two ways,  
	1. Imperative(command)  
	2. Declarative (Manifest file) - can be reuse - in real time we go with declarative
 
By default, one pod has one container, if required we can create, if you create multiple containers in a single pod, all containers inside the pods will share the same volume


### 1.1. Imperative(command)
  ``` kubectl run pod1 --image nginx ```   [Creating  pod with name pod1 with image nginx]

  ```kubectl get pods```          [To get the pods , can use pods/po/pods also]

  ```kubectl get pod -o wide```           [To get details about the pod]

  ```kubectl describe pod pod1```        [Describe is specific command so need to mention pod_name, to get more details about the pod]

  ```kubectl delete pod pod1```         [Delete is also specific, so need to mention podname as well, to delete the pod]

  ```kubectl logs pod1```              [Logs command only works on PODS (pods centric command), and need to mention pod_name as well.]

  ```kubectl exec -it pod1 -- /bin/bash```   [to connect to the pod1 inside]  
  ```cd /usr/share/nginx/html```    
  ```cat index.html```    

```kubectl delete pod pod1```        [This will delete the pod named pod1]
   
The above approach is not advised, because don't create PODS manually , declarative approach is preffered all the time.

### 1.2. Declarative (Manifest file)
In manifest file we have these mandatory parameters  

```
apiVersion:
kind:
metadata:
spec:
```

 #### 1.2.1. What is apiVersion?
  
  apiVersion: 
        * Specifies the API version used for the Deployment object. apps/v1 is the stable API version for managing deployments.
        * Depending on Kubernetes object we want to create, there is a corresponding  code library we want to use.
      apiVersion refers to Code Library
```
POD : v1
Service: v1
NameSpace: v1
Secrets: v1
Replicaset: apps/v1
Deployment: apps/v1
jobs: batch/v1
```

  ``` kubectl api-versions```   -->to get api versions


 #### 1.2.2. What is kind ?
  
  Refers to Kubernetes object which we want to create.
```
kind: Pod
kind: Deployment
kind: Service
kind: Ingress
kind: job
```
 #### 1.2.3. What is metadata?
  
  Additional information about the Kubernetes object like name, labels etc  

  _name_ : The name of the Deployment.  
  _labels_ : Key-value pairs used for organizing and selecting objects


 #### 1.2.4. What is spec?
  
  Contains docker container related information like, image name, environment variables , port mapping etc

  How many number of pods (Replica)  
  About container and that should run on which image  
  On which port it should expose  
  Labeling the entire deployment etc  

### Sample manifest file template.
```
apiVersion: v1
kind: Pod  -- creating pod
metadata:
name: pod1 -- name of the pod
spec:  -- specifications
containers:
  - image: nginx  -- image  name
  name: cont1 -- container name
```
Create one using above template.
  ```vi pod.yml```
```
apiVersion: v1
kind: Pod
metadata:
name: pod1
spec:
containers:
  - image: nginx
  name: cont1
```

```kubectl create -f pod.yml```    [ To create a pod using manifest]  
```kubectl get pods -o wide```     [ To get more details about pods]

```kubectl describe pod pod1```    [ To describe a specific pod ]

```kubectl delete pod pod1```      [ To delete the pod ]

DRAWBACK: once pod is deleted we can't retrieve the pod.
---------
If any pod deleted, it is deleted, no HA, for HA use ReplicaSET also called RS

(Additional/optionals)
		KUBECOLOR:
```
wget https://github.com/hidetatz/kubecolor/releases/download/v0.0.25/kubecolor_0.0.25_Linux_x86_64.tar.gz
tar -zxvf kubecolor_0.0.25_Linux_x86_64.tar.gz
./kubecolor
chmod +x kubecolor
mv kubecolor /usr/local/bin/
```

```kubecolor get po```

```kubectl create -f pod.yml```

```kubecolor get po```

------------------------------------------------------------------------------------
  
_Problem with using or defining only pods:_

  If any pod deleted, it is deleted, no HA, for HA use ReplicaSET also called RS  
  To achieve High availability ( Slef or auto healing ) we use REPLICASETs over PODS.  
  
-------------------------------------------------------------------------------------

# 2. REPLICASET :

This is used for managing multiple replicas of pod to perform activities like load balancing and autoscaling

Setup minikube with ubuntu 24 as per installation process

It will create replicas of same pod.

we can use same application with multiple pods.

Even if one pod is deleted automatically it will create another pod. It has self healing mechanism

Depends on requirement we can scale the pods.

_**Working flow:**_ :  
Create replica sets, replicasets will take care of the pods, pods will take care of the containers.  
i.e. Replicasets ----> Pods -----> Containers.

### 2.1 LABELS:


As we are creating multiple pods with same application, all these pods have different names but how to group all of them as we have 1 application with multiple pods. So we can give a label to group them  

Individual pods are difficult to manage because they have different names  
so we can give a common label to group them and work with them together

### 2.2 SELECTOR


It is used to select pods with same labels  

For replicaset use apiversion as apps/v1  

how to find the apiresources to write in manifest file  


Template:  
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
name: ib-rs    ---------- Name of the Replicaset
labels:
	app: bank
spec:     ------------- this spec is for PODS
replicas: 3   --------- how many number of pods
selector:
	matchLabels:  -Ensures only pods with label app: bank are part of this Replicaset. if there is any pod with label bank, it will be a part of this replicaset
	app: bank
template:            ------------ Ensures the pods get labeled as app: bank
	metadata:
	labels:
		app: bank
	spec:  ----------------- this spec is for containers
	containers:
	- name: cont1
		image: trainerreyaz/ib-image:latest
```


```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
name: ib-rs
labels:
	app: bank
spec:
replicas: 3
selector:
	matchLabels:
	app: bank
template:  
	metadata:
	labels:
		app: bank
	spec:  
	containers:
	- name: cont1
		image: trainerreyaz/ib-image:latest
```


```kubectl create -f replicaset.yml```		# to run the replicaset inorder to creats pods with desired replicas.

```kubectl get replicaset```				# to list the replicasets
		(or)
  ```kubectl get rs```

```kubectl get rs -o wide``` 				# [This command will get more details about ReplciaSets]

```kubectl describe rs ib-rs```  			# [This will describe about internetbanking-rs ]

```kubectl get pods```

```kubectl get pods --show-labels```   		# [This will list the pods with Labels]



If you delete any pod, automatically new pod will be created, if you want to watch live, open aother duplicat terminal 

```kubectl get pods --watch```  --> run this on duplicate terminal and watch this everytime you perform any operations/actions.

In the original terminal, 

```kubectl get pods```

```kubectl delete pods pod-id```      [First new pod create and the existing pod will be deleted]

```kubectl get pods --show-labels``` [New pod got created, this is called ReplicaSet, if one pod delete , another pod will get created automatically]

```kubectl get pods -l app=bank```  [This will list all the pods with label bank, l = label]

```kubectl delete pods -l app=bank```  [To delete all the pods wit label bank]

> [!NOTE]
> Replicaset will take image details from manifest file -- replicaset.yml.

----------------------------------

### 2.3 SCALE REPLICAS :

* Scale Out   
``` kubectl get pods --watch```   ---> Do this in another terminal for watch.  
```kubectl get rs```  				#[To list the replicasets]  
```kubectl scale rs/ib-rs --replicas=10```  	#[Now see pods creating live]  

* Scale In  
```kubectl scale rs/ib-rs --replicas=5```  [Now see pods creating live]  

_LIFO: LAST IN FIRST OUT.    
IF A POD IS CREATED LASTLY IT WILL DELETE FIRST WHEN SCALE IN_

> [!NOTE]
> This Scale out and in is manual, later we learn how to automate.


### 2.4 Roll out concept:  (wont work with replicasets object kinds)

Lets say that I have a _manifist.yml_ file that was defined to use nginx image.  
But now we need to update it to apache2 iamge.  
There are 2 ways we can think of doing this.  
	1. Edit the _manistfist.yml_ file directly, and re-run the file. This will throws and error saying the object already exist. (NOT RECOMMENDED).    
 	2. To edit the replica (not .yml file).    
  	```kubectl edit rs/ib-rs```  ---> command to edit the ib-rs replicaset (not .yml file).     
   	we can edit it as needed and save it. This is the **recommended way** as it wont give any errors.    

But the actual issue of image update will not reflected on the pods. Even if we scale out the replica set, the new pods will contain the same old image.  

So with REPLICA SETS,   
_**Advantage**_  
	* self healing  
	* scaling  
_**Drawbacks**_:  
	* we cannot roll in and roll out, we cant update the applications using ReplicaSet, lets use DEPLOYMENT  
_**Working flow:**_ :  
	Create replica sets, replicasets will take care of the pods, pods will take care of the containers.  
	i.e. Replicasets ----> Pods -----> Containers.

-------------------------------------------------------------------------------------------------------------------

### Optional / For knowledge:
2.5 Initial version of ReplicaSET  = ReplicationController :  
Same as ReplicaSet. It also used for handling multiple replicas of specific pod. But it doesn't contain selector and its child field matchField. matchField where it will search for pods based on a specific label name and adds them to Cluster
																									

		Kind : ReplicationController

	This below code doesn't contain matchLabels Field
	******************************************

		apiVersion: apps/v1
		kind: ReplicationController
		metadata:
		name: ib-rs
		labels:
			app: bank
		spec:
		replicas: 3
		template:  
			metadata:
			labels:
				app: bank
			spec:  
			containers:
			- name: cont1
				image: trainerreyaz/ib-image:latest


Replication Controller
---------------------

The Replication Controller is the original form of replication in Kubernetes

The Replication Controller uses equality-based selectors to manage the pods.

The rolling-update command works with Replication Controllers

Replica Controller is deprecated and replaced by ReplicaSets.

Replica Set  
------------

ReplicaSets are a higher-level API that gives the ability to easily run multiple instances of a given pod

ReplicaSets Controller uses set-based selectors to manage the pods.

The rolling-update command won’t work with ReplicaSets.

Deployments are recommended over ReplicaSets.


-----------------------------------------------------------------------------------------------------------------------------

# 3. Deployment

Kubernetes deployment is a high-level resource object by which you can manage the deployment and scaling of the applications while maintaining the desired state of the application. You can scale the containers with the help of Kubernetes deployment up and down depending on the incoming traffic.   
If you have performed any rolling updates with the help of deployment and after some time if you find any bugs in it then you can perform rollback also. Kubernetes deployments are deployed with the help of CLI like Kubectl it can be installed on any platform.  

Working strategy: Create deployments, deployments take care of replica sets, replica-sets take are of PODS and PODS  will take care of Containers.  
i.e. Deployments ----> Replica sets ----> PODS ----> Containers

So on the whole,   
	we mostly works on the deployment layer as it can take care of the layers under it

The manifest.yml file is as similar as for replicasets, the only major difference is that we mention kind as deployment rather than replicaset.  
Just change  "kind: Deployment" and "name: ib-deployment"

Create a manifist file with name deployment.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 3
  selector:
    matchLabels:
      app: bank
  template:
    metadata:
      labels:
        app: bank
    spec:
      containers:
      - name: cont1
        image: trainerreyaz/ib-image:latest
```

```
kubectl create -f deployment.yml
```
To run the manifist file named as deployment.yml

```
kubectl get deploy
```
or
```
kubectl get deployments
```

```
kubectl get deploy -o wide
```

```
kubectl get rs
```  
As the deployment also creates replica sets, to list that replicasets.

```
kubectl describe deploy ib-deployment
```

```
kubectl get pods
```

Now lets scale out and Scale IN  

### Scale Out

```
kubectl scale deploy/ib-deployment --replicas=10
```  
```
kubectl get pods
```
Check the no.of pods


### Scale In

```
kubectl scale deploy/ib-deployment --replicas=5
```   
```kubectl get pods```  

What ever Replica set is doing Deployment is also doing the same thing

if you delete any pod, it will create immediately automatically
```
kubectl get pods
```  
```
kubectl delete pod pod-id
```  
```
kubectl describe pod -l app=bank | grep -i ID
```  
(or)  
```
kubectl describe pods | grep -i image
```
It shows all internetbankingrepo image

 Now what Deployment do additional thing is to change the application.  

First watch pods in another terminal  
```
kubectl get po --watch
```  

```
kubectl edit deploy/ib-deployment
```
Edit the deployment object to use another image

watch pods in another terminal , first it will create a new pods and then terminate old ones
```
kubectl describe pods | grep -i image
```
you can see now mobilebanking image

```
kubectl get events --sort-by=.metadata.creationTimestamp
```



### 3.1 RollOut Few commands

```
kubectl rollout history deploy/ib-deployment
```

```
kubectl rollout undo deploy/ib-deployment
```
It will go back to previous application / image

```
kubectl get pods
```
Pods are terminating and creating new pods with new image, this was not possible in ReplicaSet

```
kubectl describe pods | grep -i image
```

```
kubectl rollout pause deploy/ib-deployment
```
it is like lock, cannot undo, cannot rollout to previous.  

```
kubectl rollout undo deploy/ib-deployment
```
not possible

```
kubectl rollout resume deploy/ib-deployment
```

use following command to make it possible.
```
kubectl rollout undo deploy/ib-deployment
```


```
kubectl rollout status deploy/ib-deployment
```


### 3.2 Deployment has following Strategies:  

* _Rolling update_: This is **Default**, When you update the application, Rolling update will delete one pod and create . One by one
* _Recreate_: Delete all pods and create again- Downtime. Not recommended
* _Blue-Green Deployment_: 🟦 Blue = old version, 🟩 Green = new version, Switch traffic only when the new version is fully ready
* _Canary Deployment_:  Roll out new version to a small set of users, then gradually increase.

Out of these, Rolloing and Recreating were Kubernetes native, meaning that K8S will do that without manual intermention.  
While Blue-Green and Canary deployments require manual work.

Example: see strategy: Recreate

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: bank
  template:
    metadata:
      labels:
        app: bank
    spec:
      containers:
      - name: cont1
        image: trainerreyaz/ib-image:latest
```
If you want port to expose , use below code
```
spec:
containers:
- name: cont1
image: trainerreyaz/ib-image:latest
ports:
- containerPort: 8080   # Port inside the container
  hostPort: 8080        # Port on the host machine
```

##### 🔹 Explanation of Changes

✅ containerPort: 8080 → The app runs inside the container on port 8080.  
✅ hostPort: 8080 → The container maps its 8080 port to the same port on the host machine.  

##### 🔹 Important Notes on hostPort

hostPort directly binds the container port to the host.  
It works only on worker nodes where the pod runs.  
If multiple pods run on the same node, you cannot use the same hostPort for all of them.    

###### Recommended alternative: Instead of hostPort, use a Kubernetes Service (NodePort or LoadBalancer) to expose the Deployment.  


By default, a ReplicaSet (RS) only ensures that a specific number of Pods are running. However, it does not provide network access to those Pods externally.

✅ To access your application from the internet, you need a Kubernetes Service.

**Example files for exercise:**

*🚀 Kubernetes Manifest: 1 Pod with 2 Containers*  
In Kubernetes, a Pod can run multiple containers that share the same network and storage. Here’s how to create a single Pod with two containers inside it.

`vi pod-two-containers.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  name: two-container-pod
  labels:
    app: multi-container
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
  - name: busybox-container
    image: busybox
    command: ["sh", "-c", "while true; do echo 'Hello from BusyBox'; sleep 10; done"]
```


**-c:** Tells sh to execute the following string command

```
kubectl apply -f pod-two-containers.yaml
```

```
kubectl get pods
```    
we get single pod but 2/2 meaning, 2 containers

```
kubectl logs two-container-pod -c busybox-container
```

```
kubectl logs two-container-pod -c nginx-container
```

```
kubectl exec -it two-container-pod -c nginx-container -- sh
```
-c: container  

`cd /usr/share/nginx/html
`

```
kubectl exec -it two-container-pod -c busybox-container -- sh
```
`ls`



# 4. Kubernetes JOBS:
   
Kubernetes Jobs are resources used to run batch tasks in a Kubernetes cluster.   
They ensure that a specified number of Pods successfully complete a task before marking the Job as done.   
Jobs are crucial for executing one-time or recurring tasks, such as data migrations, backups, or batch processing.  

### 4.1 Types of Kubernetes Jobs:

#### 4.1.1 Non-parallel Jobs:

These Jobs execute tasks sequentially /one by one, with only one Pod started unless it fails.   
The Job completes as soon as its Pod terminates successfully.   
It has completions parameter, Like _completions: 3_. The Job completes only when 3 Pods have successfully run

#### 4.1.2 Parallel Jobs with a Fixed Completion Count:

In these Jobs, multiple Pods run simultaneously to complete a task.  
The Job is considered complete when a specified number of Pods successfully complete their tasks.  
It has completions and Parallelism.
  _completions: 6
  parallelism: 3_

In the above example three pods are executing at a time, since we mentioned Parallelism = 3, so once the three pods are completed their tasks then next three will start execution.


#### 4.1.3 Parallel Jobs with a Work Queue:

These Jobs dynamically scale Pods based on workload.  
Pods coordinate with each other or external services to fetch and process tasks from a shared queue.

 **_Real-world Use Cases_**: 
	* _Non-parallel Jobs_: Running one-time administrative tasks, such as database migrations or system updates.
	* _Parallel Jobs with a Fixed Completion Count_: Processing large datasets, where data is split into chunks and processed concurrently by multiple Pods.
	* _Parallel Jobs with a Work Queue:_ Handling variable workloads, such as incoming requests in a web application, where Pods scale dynamically based on demand.

```
vi nonparallel.yml

apiVersion: batch/v1
kind: Job
metadata:
  name: non-parallel-job
spec:
  template:
    metadata:
      name: non-parallel-pod
    spec:
      containers:
      - name: non-parallel-container
        image: busybox
        command: ["echo", "Hello from the non-parallel job"]
      restartPolicy: Never
  completions: 3
```

_busybox:_ BusyBox is a single executable that bundles multiple Unix utilities (like sh, ls, cat, echo, wget, grep, etc.). It is widely used in minimal containers because of its small size (~1MB) and fast startup time.

_completions: 3_ → The Job completes only when 3 Pods have successfully run.

--> _restartPolicy_: Always, OnFailure, Never

| Header 1     	| Explanation    	|	Usage    |
| ------------ 	| ----------------------|----------------|
| Always 	|  Always restarts the container if it exits|Default for Deployments & ReplicaSets |
| OnFailure 	|Restarts the container only if it exits with an error (non-zero exit code).| Jobs & CronJobs |
|   Never       |Never restarts the container, even if it fails. | Jobs & CronJobs (One-time execution) |                                     

```
kubectl apply -f nonparallel.yml
```

```
kubectl get pods
```

```
kubectl get jobs
```

```
kubectl logs -l job-name=non-parallel-job
```

kubectl logs non-parallel-job-2vvf9
```
kubectl logs podname
```


```
kubectl delete job jobname
```

In above example the pods are executing one by one.


#### 4.1.4 Parallel Jobs with a Fixed Completion Count: 

Use the below simple manifest file for testing Parallel Jobs with a Fixed Completion Count jobs:

```
vi parallelfixed.yml
```


```
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel-fixed-count-job
spec:
  template:
    metadata:
      name: parallel-fixed-count-pod
    spec:
      containers:
      - name: parallel-fixed-count-container
        image: busybox
        command: ["echo", "Hello from the parallel-fixed-count job"]
      restartPolicy: Never
  completions: 6
  parallelism: 3
```

_completions: 6 _→ The Job completes only when 6 Pods have successfully run.
_parallelism: 3_ → Runs 3 Pods at the same time.


```
kubectl apply -f parallelfixed.yml
```

```
kubectl get pods
```

```
kubectl get jobs
```

```
kubectl logs -l job-name=<job-name>
```

```
kubectl logs parallel-job-2vvf9
```
parallel-job-2vvf9 = Pod-name of one of my pods.

```
kubectl delete job jobname
```

In the above example three pods are executing at a time, since we mentioned Parallelism = 3, so once the three pods are completed their tasks then next three will start execution.

#### 4.1.5 Parallel Jobs with a Work Queue 

Use the below simple manifest file for testing Parallel Jobs with a Work Queue jobs:

```
vi parallel-work-queue-job.yaml
```

```
apiVersion: batch/v1
kind: Job
metadata:
  name: parallel-work-queue-job
spec:
  template:
    metadata:
      name: parallel-work-queue-pod
    spec:
      containers:
      - name: parallel-work-queue-container
        image: busybox
        command: ["echo", "Hello from the parallel-work-queue job"]
      restartPolicy: Never
  parallelism: 3
```

```
kubectl apply -f parallel-work-queue-job.yaml
```

```
kubectl get pods
```

In the above example 3 pods are started executing at a time since we mentioned Parallelism = 3 and we didn’t mention any specified number of Completions.
                           

# 5. 🚀 Kubernetes CronJob Example

A Kubernetes CronJob is used to schedule jobs to run at specific times, just like a Linux cron job. It is useful for tasks such as backups, periodic data processing, or sending scheduled reports.

This CronJob prints "Hello from Kubernetes!" every minute.

```
vi cron.yml
```


```
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cronjob
spec:
  schedule: "*/1 * * * *"  # Runs every minute
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello-container
            image: busybox
            command: ["sh", "-c", "echo Hello from Kubernetes!"]
          restartPolicy: Never  # Ensure job runs only once per schedule
```

```
kubectl apply -f cron.yaml
```

```
kubectl get cronjobs
```

```
kubectl describe cronjob cron
```


🔹 View Logs of Last Run
---------------------------

```
kubectl get pods
```
# Get the latest pod name

```
kubectl logs <POD_NAME>
```

```
kubectl get cronjob
```

```
kubectl delete cronjob
```



-----------------------------------------------------------------------------------

# 6. NAMESPACES.

> [!NOTE]
> If you are very new to using multi node cluster and are not fine to go with multi node cluster, then go with minikube (single node cluster) than KOPs.
Name space is the means by which we segregate to differenciate different enviroments/teams.

If we have multiple teams, say _Development team_, _Testing team_, _Prepod team_ and _prod team_. and all of these teams want to work with kuberetes, then instead of going with multiple clusters of kubernetes, we just create different **namespaces** for each team in a single cluster.


Good thing about namespaces is that budefault, they cannot talk with each others. so complete isolation from each other.  

In kubernetes, if we are using a cluster, there are few default namespaces already present. they are:  
| Types  	|  Purpose or work |
| ---------- | -----------------|
| default 	|  This is default namespace. If no namespace is mentioned, all objects are created here |
| kube-node-release |	It will store the object which is taken from one namespace to another |
| kube-public	|All Public objects are stored here, generally namespace are private, if you want common public namespace |
|kube-system	| By default K8S will create some object, those are stored here |

> [!NOTE]
> 1. Objects here refers to deployments, replica sets, pods, services. Every thing in K8S are treated as objects.  
> 2. Default objects that K8S will createa are apiserver, controller manager, schedular, etcd, kubelet, kubeproxy or simply, every component of kubernetes cluster is going to create in the form of POD, All these PODS are stored in Kube-System Namespace.


`kubectl get namespace`		--> to get the list of namespaces.  
`kubectl get ns`		--> same as above.  

Bydefault, pods will be created in default name space if no namespace is mentioned.  
`kubectl describe pod`		--> can see the namespace details.  
`kubectl get pods -A`		--> to get pods of all namespaces.  
`kubectl get pods -n namespace_name` --> to get the pods of specific namespace, use default, kube-public as namespace.


##### Lets create name spaces.
`kubectl create ns dev`		--> creating a namespace named dev.  
`kubectl get ns`		--> look for the created namespace in results.  

How to check in which namespace you are currently are  
`kubectl config view`  

Responce will look like this, 
```
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://api-param-k8s-local-ois0ae-597c9eddbfb36165.elb.ap-south-1.amazonaws.com
    tls-server-name: api.internal.param.k8s.local
  name: param.k8s.local
contexts:
- context:
    cluster: param.k8s.local
    user: param.k8s.local
  name: param.k8s.local
current-context: param.k8s.local
kind: Config
preferences: {}
users:
- name: param.k8s.local
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
```
Note that it wont contain any namespace tag or section. That is the very sign that says we are in default namespace.  

Bydefault we will be in default namespace and hence all pods are created here in the default namespace.

Lets switch to dev namespace.  
`kubectl config set-context --current --namespace=dev`  

conform the switch was sucess.  
`kubectl config view`  

lets create pods in pod namespace.  
`kubectl run dev1 --image nginx` 	--> creating nginx pod with pod name as dev1.
`kubectl run dev2 --image nginx`	--> creating nginx pod with pod name as dev2.

As we have pods in the default namespace and dev namespace, lets use _get pods_ command and see which pods will be listed  
`kubectl get pods`  
only the pods belongs to dev namespace are listed.  


As I have mentioned that use of namespaces will have complete isolation from ine another, but with use of `kubectl get pods -A` we can get all pods of all namespaces.  
Also, we can shift from one namespcae to another namespace, then how come its a complete isolation!!.  

In this case, anyone can access/delete/create pods in any namespace. Which is not good. for this we need restrict users to access namespaces using RBAC ( Role based Access Control ).

# 7. RBAC.

# 8. SERVICES.

The very use of services is to enable the communication.  
When I say communication I mean two aspects,   
* _**Communication in between cluster**_  
	When two pods need to work depending on another ( say i have UI in one pod that connects to database in 		another POD) then the two pods need to connect or communicate and this is internal aommunication.  
 * _**Communicatio to the outside world.**_  
	I also need ot make the UI presented to my users to use it, to interact with it. This is outside the cluster communication.


So services allow you to expose your application running on pods to other components within cluster or to the external users.  

Services porvide a stable IP address and DNS name for the set of PODS, which might chnage over time due to scaling or updates.

#### Key concepts of SERVICES in Kubernetes:
1. **_Cluster IP_** :  
   This is default type of service. It exposes the Service on a cluster-internal IP. Other services within the same Kubernetes cluster can access the Service, but it is not accessible from outside the cluster.  
   This creates a connection using an internal Cluster IP address and a Port.

2. **_NodePort_** :  
   This type of Service exposes the Service on each Node’s IP at a static port. A NodePort Service is accessible from outside the cluster by hitting NOdeIP:NOdePort  
   when node port is created, kube-proxy exposes a port in the range 30,000-32,767.  
   We can explicitly mention the port no. else k8s takes are of assigning the port no. but anyhow, the port no. should be in same rangee.

3. **_TargetPort_** :  
   Pod Container port. Pod's container listens on applicationn port Ex: 80 or 8080, if you dont use this line, K8s will assign default 80 port.  

4. **_LoadBalancer_** :  
   This Service type exposes the Service externally using a cloud provider’s load balancer. It is typically used in cloud environments like AWS, GCP, or Azure.  
   A LoadBalancer is a Kubernetes service that:  
	* Creates a service like cluster IP,  
	* Opens a port in every node like nodeport,  
	* Uses a load balancer implementation from your cloud provider.

> [!NOTE]
> We dont need to explicitly mention the loadbalancer's url or to create a load balancer in aws account and copy paste the url. k8s will take care of this as it creates its own VPC and respective needed resources in this VPC.
   

Services in k8s are created on top of deployment. so we cannot a standalone service without any deployments.  
Services are defined with _manifist.yml_ file.


Lets creat a deployment and then create service on top of it.  
We can define both deployment and service in a single manifist.yml file.  

#### 8.1 For clusterIP exercise.    
`vi nginxapp.yml`  

```
appVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginxapp
spec:
  replicas: 3
  selector:
     matchLabels:
        app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      containers:
        - name: cont1
          image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: nginxapp-service
spec:
  type: ClusterIP
  selector:
    app: nginxapp
  ports:
    - port: 80
      targetPort: 80
```

In the file, -port: 80  = expose the service on port 80.   
targetPort: 80  = Pod's container listens on 80, if you dont use this line, k8s will assign defalut 80 port.


How Traffic Flows  
	_Client connects to my-service:80  
	Service forwards request to the Pod’s container at 8080 if 80 (because targetPort: 8080 / 80)  
	The container in the Pod listens on 8080 / 80 and processes the request._  


Run the nginxapp.yml file.  
`kubectl create -f nginxapp.yml`  

get list of services.  
`kubectl get svc`  

get list of pods.  
`kubectl get pods -o wide`  

describe the nginxapp-service that we have created.  
`kubectl describe svc/nginxapp-service`   --> nginxapp-service is the name of the service that we have created.  

As of now we have created clusterIP but with this we can anly estlabish internal communication but not outside world communication.  
`kubectl delete -f nginxapp.yml`   --> to delete the created deployment and service.


#### 8.2 For NodePort service.  
`vi nginxapp.yml`  
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginxapp-deployment
  labels:
    app: nginxapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      containers:
      - name: cont1
        image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: nginxapp-service
spec:
  type: NodePort   # Type Nodeport, but if you dont mention port number under ports section, K8s will assign random port
  selector:
    app: nginxapp
  ports:
    - port: 80
      targetPort: 80  # Ensure this matches the container's port, if you dont use this line, K8s will assign default 80 port
```

create or run the file.  
`kubectl create -f nginxapp.yml`  

lets describe service that we have created.  
 `kubectl describe svc/nginxapp-service`  


```
kubectl get services
```
For this command, we can see the port numbers of nodes that K8S assigned on our behalf.  
```
NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP   100.64.0.1     <none>        443/TCP        45m
nginxapp-service   NodePort    100.65.75.28   <none>        80:30301/TCP   4m34s
```
This is the responce in my case.  
Note the Node portnumber assigned is 30301 in this case.  
So, if I want to access the nginx application then i need to simply put,   
 `http://ip:nodePortNumber`    ( ip is the node Ip  = worker nodes ec2 instance public IP ).    
 It may not woork as the cluster created new VPC and new SG for cluster, we need to allow the inblound rules for the port.  So go to respective Security Group, allown alltraffic for all IPs. 

 If we want to explicitly mention the Nodeport, then edit the above file and add `nodePort: 3122` at the end of ports section.  
 Remember the nodeports range should be between 30,000 to 32767.  

How traffic flows: 
	_Client accesses http://<NodeIP>:31234  
	Traffic reaches Kubernetes Node on nodePort: 31234  
	The service forwards it to port: 80 (internal Service port)  
	Then it forwards the request to targetPort: 80 (inside the Pod’s container)._

`kubectl delete -f nginxapp.yml` 	--> to delete the deployment and services from file.  

As we cannot provide the Ips to users, we need to use load balancers.  
Along with load balancer, use Route53 to give the url a domani name.  


 #### 8.3 For LoadBalancer  
 `vi nginxapp.yml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginxapp-deployment
  labels:
    app: nginxapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      containers:
      - name: cont1
        image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: nginxapp-service
spec:
  type: LoadBalancer
  selector:
    app: nginxapp
  ports:
    - port: 80
      targetPort: 80  # Ensure this matches the container's port, if you dont use this line, K8s will assign default 80 port
      nodePort: 31433 # If you dont mention target and nodeports, K8s will generate random nodeport and target port 80
```

Create the deployment and service.  
`kubectl create -f nginxapp.yml`  

describe the service.  
`kubectl describe svc/nginxapp-service`  

```
kubectl get svc		  
```
note the loadbalancer url.   
use the url and access the application.  

# 9. Auto scaling with Kubernetes METRIC SERVER  (HPA Horizontal Pods Autoscaling).
So far we hvae been doing scaling of pods, replicas, deployments manually.  
```
kubectl scale object --replicas=10
```

But we need auto scaling. This can be acheived by monitoring metrics like CPU, Memory etc.

_**Kubernetes Metric Server**_, is a scalable efficient source for monitoring the overall health and performance of a kubernetes cluster, providing the data need for kubernetes features like **HORIZONTAL POD AUTOSCALING** **( HAP )** and Kubernetes Dashboard.  

**Key features**:  
#### 9.1 Resource Metrics:
Collects CPU and memory usage metrics from kubelets and provides aggregated metrics at the node and pod level.  

#### 9.2 Autoscaling:  
Enables features like the Horizontal Pod Autoscaling (HPA), which automatically adjusts the no. of pods in a deplooyment based on the observed CPU or memory utilization.

#### 9.3 Kubernetes Dashboard:  
The metrics server provides the resources usage data dislayes in the kubernetes Dashboard.

#### 9.4 Kubelets:  
Each nodes in a kubernetes cluster runs a kubelet that periodically clooects resources usage statics from the node and the containers running on it.

#### 9.5 Metrics server: 
Metrics server collects these metrics from the kubelets and stores them in memory, aggregating them to be accessed by other components like HPA.

_So in short:_  
	Info like CPU, Memory from each PODS, Nodes -------> collected by Metrics Server in K8S.  
	This will be done on every 15 sec.  ( we can say near real time.)  

Well, the Metrics server is not default in K8S, its like a addon plugin.  
We need to install in the master server.  

Install the Metrics Server from github official repo's .yml file.    
```
kubectl create -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability-1.21+.yaml
```


Lets check the metrics of resources.  
```
kubectl top pods
```

```
kubectl get nodes
```


With this lets acheive Autoscaling.

**Auto-Scaling**:

HPA - to scale no. of pods based on metrics usage.  The metric can be CPU, Memory or even custom metrics.  
Remember that its PODS that we scale not NODES here.  

Before scale In, process will wait for few mins to scale-in to complete the traffic requests this is called COOLING PERIOD   

In Kubernetes, the cooling period for the Horizontal Pod Autoscaler (HPA) is the amount of time the HPA waits after a scale event before triggering another scale event.  

Scaling can only be done on/for scalable objects like Deployments, Replicasets, Pods, Replication Controller.  

_Replication controller is the older version of Replicasets._


So lets create a deployment object and run it.
```
vi nginxapp.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginxapp-deployemnt
  labels:
    app: nginxapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      containers:
       - name: cont1
         image: nginx
```

Create the deployment.  
```
kubectl create -f nginxapp.yml
```

Get the pods list.  
```
kubectl get pods
```

Get the Metrics of pods.  
```
kubectl top pods
```

Now lets auto scale the pods,  
```
kubectl autoscale deployment nginxapp-deployment --cpu-percent=20 --min=1 --max=10
```
The commad says, if the cpu percentage goes up more than 20 then use auto scaling to scale up to 10 pods.  

```
kubectl get hpa
```
This wil show cpu: 1% / 20% , now cpu percent is 1%, it will take time.  

You can stress the pods by installing stress inside the pods.  
First connect to a pod interactive mode.  
```
kubectl exec -it nginxapp-deployment-XXXXXX-XXXX -- /bin/bash
```
Install _STRESS_, inside the pod.  
```
apt update
apt install stress
stress --cpu 8 --io --vm 2 --vm-bytes 128M --timeout 60s
exit
```

Meanwhile duplicate the present tab and watch the pods creation lively.

Delete the stress or completely delete the deployment object's yml file.


Lets do the same autoscaling but now with Manifist file ( declaretive mode) most used way.  
---------------

Create deployment.  
```
vi auto.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginxapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      containers:
      - name: cont1
        image: nginx
```

```
kubectl create -f auto.yml
```

```
vi hpa.yml
```

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginxapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 20
```

NOTE: we can use a single yaml file to define the deployment and autoscaling as well.  

In the scaleTargetRef section, the details of the target deployment file are to be mentioned.


```
kubectl apply -f hpa.yml
```

In the similar way as we did above, go to interactive mode into any pod, install stress and run the stress execution command.  
and look for actoscaling working.  


To delete the Metrics Server, 
```
kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability-1.21+.yaml
```

 # 10. Resource Quota.

In K8S, we seprate different teams using same cluster with _NAMESPACES_.  
By default pods that every team create will run without any limitations of **Memory** and **CPU**.  
And that not advised, it may leads to unethical over usages of resources and leads to problems like availability of resources to PROD section etc.  

We need to limit the resources with Resource Quota.  
It can limit the objects that can be created in a namespace and total amount of resources.  
pod schedular in master will check the worker nodes on cpu and memory and create a pod in it.  
we can set limits to CPU, Memory and storage aspects.  

CPU is measrured on Cores and Memory on Bytes.  
1CPU = 1000 milliCPUs.  

Limits can be given to pods and nodes also.  
Th default limit is 0.  

Requests refer to how much we want and   
Limit refer to how much max we want.  

_if you mention Request and limit, everthing will work fine.  
if you dont mention Request but mentions limit then Request = limit.    
if you dont mention Request dont mention limit , Request ! = limit_.  


Every pod in the namespace must have CPU limit, no need to mention memory, if you dont mention cpu, pod will take all cpu.  
The amount of CPU used by all pods inside namespace must not exceed specified limit.  

###### Lets create a team namde _dev_ and limit the resources to it.
1. Create a namespace.
```
kubectl create ns dev
```

2. Switch to dev namespace.
```
kubectl config set-context --current --namespace=dev
```

verify that we are in dev namespace.
```
kubectl config view
```

Resource quotas are assigned thorugh manifest.yml files, same as deployments, services, replicasets.  

3. Creating resource quota for dev team.  
create a dev-quota manifest file.  
```
vi dev-quota.yml
```

```
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: dev
spec:
  hard:
    pods: '5'
    limits.cpu: '1'
    limits.memory: 1Gi
```

Compherend the dev-quota.yml file:  
	1. _kind: ResourceQuota_.  
 	2. _namespace: dev_, represents that we are refering the dev namespace.  
  	3. _pods: '1'_, represents that we are limiting the pods count to max of 5.  
   	4. _limits.cpu: '1'_, represents that we are limiting the CPU across all containers of that namespace to 1CPU.  
    	5. _limits.memory: 1Gi_, similar to CPU limit, memory is limited to 1Gb for the whole namespace.  


Run the file.  
```
kubectl create -f dev-quota.yml
```


lets look at the usage quota of that name space.  
```
kubectl get quota
```

resluts will look like this,   
```
NAME        AGE   REQUEST     LIMIT
dev-quota   69s   pods: 0/5   limits.cpu: 0/1, limits.memory: 0/1Gi
```
It is because that we have not created any pods in that namespace.  


4. Lets create a deployment object,   
```
vi deploy.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginxapp-deployment
  labels:
    app: nginxapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      containers:
        - name: nginx-cont
          image: nginx
          resources:
            limits:
              cpu: '1'
              memory: 512Mi
```

Note:  
we have mentioned limits to the container as to use 1 cpu and 512 mb of size and it is necessesary to do so.    
Running this deployment object, will create only 1 pod eventhough we have mentioned replicas as 3.  
It is because that a single container/pod takes up 1 cpu and 512 mb of size then the remaining resources _(0 cpus, and 488Mb memory)_ is not sufficient to set up another POD.  

So it is vital to check the resource limits and define the pods limits and replica counts.  

so edit the above file to _cpu: 0.3_ and _memory: 300Mi_.  
Now create the object.  
```
kubectl create -f deploy.yml
```

Now list pods  
```
kubectl get pods
```

Check the quota usage  
```
kubectl get quota
```

##### Cleanup
At last delete the deployment, delete the resource quota, and the namespace.  
```
kubectl delete -f deploy.yml
```
```
kubectl delete quota/dev-quota
```
```
kubectl delete namespace/dev
```

# 11. Persistent Volumes (PV) in Kubernetes.

### 11.1 Stateless and Statefull.

------------------------------------------------------------

#### Stateless:
If we delete the pods, the data in those pods will be lost for ever.  
It is because the data was stored locally in the pods ans instances.  

#### Stateful:
If we delete the pod, and still data is persistent because we can store the data in external storages lik AWS EBS.  

--------------------------------------------------------------------

### 11.2 Persistent volumes: 

K8S Persistent Volumes (PVs) provide a way to manage durable storage for applications running in a K8S cluster.  
Unlike ephemeral storage tied to the lifecycle of a pod, Persistent Volumes exist independently of pods and remain intact even after pods are deleted.  

This makes PVs ideal for stateful applications that require persistent storage, such as databases.  
Persistent meaning permanent.  


It is created by administrator or dynamically created by storage class.  
Once a PV is created , it can be bound to a Persistent Volume Claim (PVC), which is a request for storage by a pod.  
When a pod requests storage via PVC , K8S will search for a suitable PV to satisfy the requests.  

PV is bound to the PVC and the pod can use the storage.  
If no suitable PV is found, K8S will either dynamically create a new one (if the storage class support dynamic provisioning ) or the PVC will remain unbound.  


> PV will maintain total storage , PV Is bound to PVC, Pods will ask PVC, PVC will get from PV.

 
### 11.3 Persistent Volume Claims (PVC)

To use PV we need to claim the volume using PVC.  
PVC request a PV with your desired specification(size, access, modes & speed etc) from K8S and once a suitable PV is found it will bound to PVC.  

After bounding is done to pod you can mount it as a volume.  
Once user finished work, the attached PV can be released the underlying PV can be reclaimed and recycled for future.  

if you create volume in cluster , if cluster is delete , storage is also deleted. SO use AWS EBS Volumes.  


| Component | Purpose                          |
| --------- | -------------------------------- |
| **PV**    | Admin-provisioned storage        |
| **PVC**   | User request for storage (claim) |
| **Pod**   | Uses the PVC to mount the PV     |

### 11.4 Lets exercise our learnings. 

#### 11.4.1 First, create a EBS volume from AWS with 20GB and magnetic, note the volume ID.

#### 11.4.2 Lets create a Persistent Volume with the created EBS volume.
```
vi pv.yml
```

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 20Gi
  accessModes:
     - ReadWriteOnce
  awsElasticBlockStore:
     volumeID: vol-9812360128yn128yb
     fsType: ext4
```

create the pv object.  
```
kubectl create -f pv.yml
```


```
kubectl get pv
```



Remember just a PV is not enough to access the volume by pods, we need PVC as well.  

#### 11.4.3 Lets create PVC

```
vi pvc.yml
```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
```

```
kubectl create pvc.yml
```

get pv list  
```
kubectl get pv
```
This lists both pv and pvc now.  

#### StatefulSet

A StatefulSet is a Kubernetes workload API object used to manage stateful applications. Unlike Deployments or ReplicaSets, which manage stateless pods, StatefulSets are designed for apps that require stable identities, persistent storage, and ordered deployment.  

```
vi deploy.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pvdeploy
spec:
  replicas: 1
  selector:
    matchLabels:
     app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      containers:
      - name: cont1
        image: nginx
        command: ["/bin/bash", "-c", "sleep 10000"]
        volumeMounts:
        - name: my-pvc
          mountPath: "/tmp/persistent"
      volumes:
        - name: my-pvc
          persistentVolumeClaim:
            claimName: my-pvc
```

create the object,
```
kubectl create -f deploy.yml
```

Use interactive mode to get into pod and look for volume,
```
kubectl exec -it podid-dijfowefn -- /bin/bash
```

```
cd /tmp
ls
```

```
cd persistent
```

create few files,
```
touch file{1..6}
```
add the contnent _this is from pod1_ to the file1.    
and exit form interactive mode.  

Now, delete the pod and K8S will automatically creates another pod becuase we mentioned replica (self healing).  
Access the new pod with interactive mode and look for the files that we created from previous pod in the same path.  


If we are able to see the files in the new pod, that says that deletion of pods doesnot delete the data in it === StateFull.   


### 11.5 Dynamically Provisioning.

📦 What is StorageClass!
------------------------
A StorageClass is a way to dynamically provision PersistentVolumes (PVs) in Kubernetes without manually creating them ahead of time.

When you create a PersistentVolumeClaim (PVC) that refers to a StorageClass, Kubernetes:
* Automatically creates an EBS (or other backend) volume.  
* Binds that volume to your PVC.  
* Mounts it to your Pod.


To use EBS-backed dynamic PersistentVolumeClaim (PVC) in a KOPS-managed Kubernetes cluster on AWS, follow this practical example using the built-in gp2 or gp3 StorageClass provided by AWS and KOPS.  

Note: If you directly create pvc without pv ... KOPS will create pv automatically. pv meaning , kops will create a EBS volume automatically.  

Note:  dynamic provisioning is enabled by default in KOPS

#### 11.5.1 Verify StorageClass

```
kubectl get storageclass
```

#### 11.5.2 Create a PVC  
No need to create pv. With use of storageclass set,  pv (ebs volume) will create automatically.  

```
vi ebs-pvc.yml:
```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: gp2  # or gp3 if available
```

```
kubectl apply -f ebs-pvc.yml
```

#### 11.5.3 Pod Using the PVC

```
vi ebs-pod.yml
```

```
apiVersion: v1
kind: Pod
metadata:
  name: ebs-nginx
spec:
  containers:
    - name: web
      image: nginx
      volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: ebs-volume
  volumes:
    - name: ebs-volume
      persistentVolumeClaim:
        claimName: ebs-pvc
```

```
kubectl apply -f ebs-pod.yml
```

```
kubectl get pvc  

kubectl get pv  

kubectl get pod ebs-nginx
```

You should see that the PVC is Bound, and a PV is dynamically created.  
You can also check EBS volumes in AWS Console → EC2 → Volumes — it will show as "in-use".


Delete PVC (PersistentVolumeClaim)
---------------------------------
```
kubectl get pv  

kubectl get pvc  

kubectl delete pvc my-pvc -n default
```

Delete PV (PersistentVolume)
---------------------------
```
kubectl delete pv my-pv
```


📘 Example: StatefulSet for NGINX with Persistent Storage
-----------------------------------------------------------

StatefulSet YAML

```
vi nginxstateful.yml
```

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
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
        image: nginx
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

RWO = ReadWriteOnce
ROX  ReadOnly Many
RWX ReadWriteMany

```
kubectl create -f nginxstateful.yml  [It creates 1GB volume pv is AWS EBS and automatically mount pvc to pods ]
```

```
kubectl get pv

kubectl get pvc

kubectl get pods

Delete pvc

kubectl get pvc

kubectl delete pvc www-web-0 www-web-1 www-web-2
```



# 12. ENV Variables.

It is a way to pass configuration information to containers running within pods. To set Env vars it include the **env** or **envFrom** field in the configuration file.  

### 12.1 **ENV**:  
Allows you to set environment variables for a container, specifying a value directly through CLI/ Command prompt.  

##### Example with ENV.
The following command will create a deployment object directly from CLI.  
```
kubectl create deploy newdb --image=mariadb
```
Deployment name is newdb, has only 1 pod, that too with image of mariadb ( a database image ).  

Check the pods created.  
```
kubectl get pos
```

The pod encounter in a CrashLoopBackOff, its an error, by getting the logs, we can see that its because mariadb needs password setup to run.  
```
kubectl logs newdb-79067ot-78tg
```
Error looks like this,  
>

Lets pass/set the password from CLI.  
```
kubectl set deploy newdb MYSQL_ROOT_PASSWORD=root123123
```

Now get  the pods, it will be running normally now.  


### 12.2 **ENVFORM**:  
Allows us to set the environmental variables and pass them to containers with use of _FORM FILES_ (not directly from CLI).  
FORM FILEs are 2 types:  
	* Configmaps  
 	* Secrets  

#### 12.2.1 ConfigMaps

Stores the data in key-value pair, files, or command-line arguments that can be used by pods/containers in cluster.  

Data stored in the ConfigMaps is not confidential, it provide neither any security nor any encryption.  
This is drawback of ConfigMaps and this can be overcomed by Secrtes.  

Other limitation of ConfigMaps is that it cannot be more than 1Mb size.  
If need to have more size then go for mounting volumes or use a seprate DB or file service.  

##### Example with ComfigMaps

Lets do the same example as above but now passing the password from configMap file.

Firstly, delete the deployment object created earlier if not done earlier.  
```
kubectl delete deploy newdb
```

Create a file called vars. (no file extention)  
```
vi vars
```

```
MYSQL_ROOT_PASSWORD=root123123
MYSQL_USER=admin
```

Use this file to create a CONFIGMAPS named as dbvars.  
```
kubectl create cm dbvars --from-env-file=vars
```

```
kubectl get cm
```

Use this created configmap ( dbvars ) to pass the username and pass to the mariadb pod.  
```
kubectl create deploy newdb --from=configmapps/dbvars
```

> [!NOTE]
> We can pass/mention the config file in the manifist.yml file while creating a deployment object using manifist file.

Lets check the pods creation, 
```
kubectl get pods
```
The single pod created should be running without error or retry.  


ConfigMaps are not encrypted, not have any security, lets see how.  
```
kubectl describe cm/dbvars
```
The responce for above command will return the username and password that we mention directly without any encryption.  
This is why they were preferred very less to no prefference.

Cleanup, --> delete deployment, ---> delete the cm.  
Do not erase the vars file on machine, we will use the same in secrets.

#### 12.2.2 Secretes

To store sensitive data in an unencrypted format like passwords, ssh-keys etc it uses base64 encoded format
If i save `password=root123123` then this will be encoded by k8s and if need we can decode as well.

By default k8s will create some Secrets these are useful from me to create communicate inside the cluster used to communicate with one resource to another in cluster.  
These are system created secrets, we need not to delete


	TYPES:  (to be used when creating sceret)
	Generic: creates secrets from files, dir, literal (direct values)  
	TLS: Keys and certs  
	Docker Registry: used to get private images by using the password

Lets use the same example of mariadb image and vars file that we created earlier in configmaps section.  

Create secret from vars file.  
```
kubectl create secret generic my-secret --from-env-file=vars
```
_my-secret_ is the name of the secret.  

Lets create mariadb pod using the secret.  
```
kubectl create deploy newdb --from=secret/my-secret
```
List the pod and check whether it was running without any error.  


Lets check whether secret is encoded or not if yes, then how to decode.  
```
kubectl get secret
```

!!! 
```
kubectl describe secret/my-secret 
```

```
kubectl get secrets my-secret -o yaml
```
This will gives us the keys but values as encrypted.  
Decode the keys, 
```
echo -n "LIOIGPOBbn8ybp" | base64 -d
```
`LIOIGPOBbn8ybp` is the encoded username from the `kubectl get secrets my-secret -o yaml` command results.  
Use the same command to get the decoded value of password as well.  

CleanUp --> delete the deployment object, ---> delete the secret, ---> delete the vars in the machine.

# 13. SIDE CAR

In motorcycle terms, the sidecar is the small passenger pod attached to the main bike — enhancing its function without steering the ride.  
So as similar to this,   
​A sidecar container is a secondary container that runs alongside the main application container within the same Kubernetes Pod.  
This design pattern allows you to extend or enhance the functionality of the main application without modifying its code.  
Common use cases include _log aggregation_, _data synchronization_, and _proxying network traffic_.​  


Kinds of sidecars:  
* _Adapter Design Pattern_ -- standardize the output pattern of main container.  
* _Ambassador Design Pattern_ -- used to connect containers with the outside world.
* _Init Container_ -- It initialize the first eork and exits later.


 **EXAMPLE: Log aggregation with a sidecar.**
 ------------------------

In this example, the main application container writes log data to a shared volume, and the sidecar container serves these logs over HTTP using Nginx.  

```
vi sidecar.yml
```

```
apiVersion: v1
kind: Pod
metadata:
  name: log-aggregator-pod
spec:
  containers:
    - name: app-container
      image: alpine
      command: ["/bin/sh", "-c"]
      args: ["while true; do date >> /var/log/app.log; sleep 5; done"]
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log
    - name: sidecar-container
      image: nginx
      volumeMounts:
        - name: shared-logs
          mountPath: /usr/share/nginx/html
  volumes:
    - name: shared-logs
      emptyDir: {}
```
```
apiVersion: v1
kind: Service
metadata:
  name: log-sidecar-lb
spec:
  selector:
    app: log-aggregator
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
Lets comprehend the yml file.  

1. Main application (app-container):
-----------
Alpine (a linux based bare minimal image) was used.  
A command/arg was passed that says, for every 5 sec, append the date to the `app.log` file in the path `/var/log`.  
It mounts a shared volume at `/var/log` to store log data.  

2. Side Container  (sidecar):
----------------
Uses Nginx image to serve content over HTTP.  
Also mounts the same shared volume at `/usr/share/nginx/html`.  
Hence, Nginx serves the app.log files, allowing access to the application's log data via HTTP.

3. Shared Volume (shared-logs):
-----------------------------
An emptyDir volume that is created when the Pod is assigned to a node and exists as long as the Pod is running.​  
Provides a shared storage space accessible to both containers for log data.​  

emptyDir a temporary directory that exists as long as the Pod runs.  


```
kubectl apply -f sidecar.yml
```

```
kubectl get pods
```

Step into the sidecar-container in interactive mode.
```
kubectl exec -it log-aggregated-pod -c sidecar-container --sh
```

```
cd /usr/share/nginx/html
cat app.log
```
we can see app.logs which is generated in app-container but can be accessed from side-car container using shared volumes  


```
kubectl delete -f sidecar.yml
```



# 14. INGRESS

Services in K8S are to expose the application in pods to the external users or for internal communication.  
These services are for pods that have strightforward access, meaning no path based routing.  

If we want **path based routing** then we need **INGRESS**.  
so,  
* Ingress helps to expose HTTP and HTTPS routes from outside of the cluster.
* Ingress supports Host based and Path bsed routing.
* Ingress also supports load balancing and SSL termination.

  <img width="758" height="497" alt="image" src="https://github.com/user-attachments/assets/453ec443-c62e-4638-a622-1a078807309f" />


IT redirect the incoming requests to the right services based on the web url or path in the address.  
Ingress provides encryption feature and helps to balance the load of the applications.  

> Host based routing ==> `example.com`, `api.example.com`, `admin.example.com`.  
> Path based routing ==>  `bookmyshow.com/login`, `bookmyshow.com/home`, `bookmyshow.com/theathers`, `bookmyshow.com/salarmovie`.

Only Ingress provides these features, not any other aspects of service ( clusterIP, Nodeport, Loadbalancer).


####  Installing Ingress controller.

To work with ingress, We need to install ingress controller.
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml
```

Use following commands to see what it have done.  
```
kubectl get services
kubectl get ingress
```

####  Lets create 2 deplyments.  
Deployment and service for nginx application.

```
vi nginx.yml
```


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx  
spec:
  replicas: 1
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
        image: nginx
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "NGINX APP1"
---
apiVersion: v1
kind: Service
metadata:
  name: nginx  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: nginx

```


Deployment and service for apache app.  
```
vi httpd.yml
```


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - name: httpd
        image: httpd
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "APACHE APP2"
---
apiVersion: v1
kind: Service
metadata:
  name: httpd  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: httpd
```


#### Yaml file for Ingress  

```
vi ingress.yml
```


```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: k8s-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
    - http:
        paths:
          - path: /nginx(/|$)(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: nginx
                port:
                  number: 80
          - path: /httpd(/|$)(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: httpd
                port:
                  number: 80
          - path: /(.*)
            pathType: ImplementationSpecific
            backend:
              service:
                name: nginx
                port:
                  number: 80
```
Lets create those deployments and ingerss.  

```
kubectl create -f httpd.yml

kubectl create -f nginx.yml

kubectl create -f ingress.yml
```


Lets access the applications,  go and grab the ELB from AWS ec2 console or use folowing command and note the ELB url,    
```
kubectl get ingress
```
This will give you the ALB url.  
In the Security group of respective ELB, allow the alltraffic inbound rule. 


use the url, `http://elb/nginx`   and in new tab `http://elb/httpd`  
You may be able to access the applications with path based routing.  


Cleanup :  
--> delete the ingress controller  
```
kubectl delete services ingress-nginx-controller --namespace=ingress-nginx
```

---> Delete deployments and services one by one.

##### Lets compherend the section of Ingress used above.

###### Annotations:
`nginx.ingress.kubernetes.io/ssl-redirect: "false"`: Disables automatic redirection from HTTP to HTTPS.​  
`nginx.ingress.kubernetes.io/use-regex: "true"`: Enables the use of regular expressions in path matching. ​  
`nginx.ingress.kubernetes.io/rewrite-target: /$2`: Rewrites the matched URI to the specified target.​  

###### Rules  
Traffic matching /nginx, optionally followed by a / or end of the string, and any subsequent characters ((.*)), is directed to the nginx service.​  
Traffic matching /httpd, following the same pattern, is directed to the httpd service.​  
All other traffic (/(.*)) is directed to the nginx service.​ 

 ​
###### Rewrite Target
The `nginx.ingress.kubernetes.io/rewrite-target: /$2` annotation rewrites the incoming request path to the specified target before forwarding it to the backend service. Ensure that this behavior aligns with your application's routing logic.

###### Path Matching
The pathType: ImplementationSpecific allows the Ingress controller to interpret the path matching rules, including regular expressions. Ensure that your Ingress controller supports this path type and regex patterns.
 ​


# Different states of a pod
##### 1. Running
##### 2. Terminating
##### 3. ImagePullBackOff
When a kubelet starts creating containers for a Pod using a container runtime, it might be possible the container is in Waiting state because of ImagePullBackOff.  

The status ImagePullBackOff means that a container could not start because Kubernetes could not pull a container image for reasons such as:
* Invalid image name or
* Pulling from a private registry without imagePullSecret.
* The BackOff part indicates that Kubernetes will keep trying to pull the image, with an increasing back-off delay.

Kubernetes raises the delay between each attempt until it reaches a compiled-in limit, which is 300 seconds (5 minutes).

##### 4. CrashLoopBackOff
When you see "CrashLoopBackOff," it means that kubelet is trying to run the container, but it keeps failing and crashing.  
After crashing, Kubernetes tries to restart the container automatically, but if the container keeps failing repeatedly, you end up in a loop of crashes and restarts, thus the term "CrashLoopBackOff."  


# 15. PODS Scheduling

Pod scheduling determines where workloads run in your cluster, directly impacting bothe resources utilization and application peformance.  
The scheduler evaluates numerous factors from resource requirements to node availability and affinity rules to place pods optimally across your infrastructure.   

In Kubernetes, the mechanisms that influence how Pods are scheduled onto Nodes within a cluster are:  
* Node Selector
* Node affinity
* Traints and Tolerations.

Lets talk about them one by one.

#### 15.1 Node selector.

It is the simplest form of selection constraint, allowing pods to be scheduled only on nodes with specific labels.  By specifing a NodeSelector in a pod's specification, we can ensure that the pods run only on nodes that match the given label.  

When to use!!  
For simple, direct specific constraints for pods placement based on node labels.  

Alright, lets create a deployment object, add node selector element to schedule the pod on specific node.  

```
vi deploy.yml
```


```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginxapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      nodeSelector:    
        node-name: n1-node
      containers:
      - name: cont1
        image: nginx
```

lets create deployment out of it.  
```
kubectl create -f deploy.yml
```

lets lists the pods.  
```
kubectl get pods
```

If we observe, the pods are not in the running state, WHY !!  
It is because that we have mentioned pods to be created in specific node but never labeled the node.  

so lets label the nodes first,   
```
kubectl get nodes
```
grab one of the nodes id/name, 
```
kubectl edit node i-043783332483bf9f8
```
under labels --> add `node-name: n1-node`  --> save and exit.  or we can use a direct command `kubectl label nodes i-043783332483bf9f8 node-name=n1-node`    

now list the pods and look whether they are running or not. if yes then look on which node they belongs to.  
```
kubectl get pods -o wide
```

So with NodeSelector, we are forcing kube-schedular to schedule pod on particular node. **Its hard match**. If it doesn't match, Kube scheduler will not schedule the pod. so pod will be in pending state.  

cleanup ---> delete the deployment.  

#### 15.2 Node Affinity

Node Affinity is a more expressive way to specify rules about the placement of pods relative to nodes' labels. It allows you to specify rules that apply only if certain conditions are met.   
Same as Node Selector but this has flexible way, **if matches then sechdule the pods, if not then schedule pod on any another node**.  

Node affinity has two kinds with it, 
* Preferred during scheduling Ignore during execution (soft rules) : good if happen.  
* Required during scheduling Ignore during execution (hard rules) : Must happen  : Same as NodeSelector.


Node affinity supports following operators, 
1. In
2. NotIn
3. Exists
4. DoesNotExist
5. Gt
6. Lt,   etc


##### 15.2.1 Prefered kind.
```
vi preferred.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: appache-deployment
  labels:
    app: apacheapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: apacheapp
  template:
    metadata:
      labels:
        app: apacheapp
    spec:
      affinity:
       nodeAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 1
          preference:
           matchExpressions:
           - key: node-name
             operator: In
             values:
             - n2-node
      containers:
      - name: cont2
        image: httpd
        ports:
        - containerPort: 80
```

If we glance at the `spec.affinity` of the yaml file, it describes that, prrferrible schedule the httpd pods in to the node that has node label as `n2-node`.  

Also, weight: 1: Indicates the preference weight. Higher values signify stronger preferences.​

```
kubectl create -f preferred.yml
```
list the pods,  

We can observe that, 1 pod running. Why!!  even when we mention `node-name: n2-node`, No node has this label yet, but as we used **preferred** configuration, scheduler has scheduled on other nodes.  


Note on which node the deployment was done.  


lets delete the deployment, `kubectl delete -f preferred.yml`  
Lets edit the preferred.yml and change `n2-node` to `n2-node` and run the deployment again.  

```
kubectl create -f preferred.yml
```

Now observe the pods onwhich they were deployed. They will be deployed in `n1-node`.  

##### 15.2.2
```
vi required.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginxapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginxapp
  template:
    metadata:
      labels:
        app: nginxapp
    spec:
      affinity:
       nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
         nodeSelectorTerms:
         - matchExpressions:
           - key: node-name
             operator: In
             values:
             - n2-node
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

```
kubectl create -f required.yml
```

```
kubectl get pods -o wide   
```
Pod are not running, it is in **pending** state because, required is `n2-node`, but no node has label as `n2-node`.  

cleanup  
`kubectl delete -f required.yml`    



#### 15.3 TAINTS and TOLERANCE  

Use analogy like Lock (taints) and Key (tolerance)

Taints are like locks on the doors of your house, restricting access to certain pods (visitors).  
Tolerations are like keys that pods (visitors) can carry to bypass the locks (taints) and be scheduled on nodes.  


In Kubernetes, taints and tolerations work together to control the scheduling of Pods onto Nodes. Taints are applied to Nodes to prevent certain Pods from being scheduled on them, while tolerations are applied to Pods to allow them to be scheduled on Nodes with matching taints. ​  

Example Scenario: Dedicated Nodes for Specific Workloads
---------------------------------------------------------
Suppose you have a Kubernetes cluster with Nodes equipped with specialized hardware, such as GPUs, intended exclusively for machine learning workloads. To ensure that only Pods requiring GPU resources are scheduled on these Nodes, you can use taints and tolerations.

```
kubectl edit node i-0e6b67bf1b00383be
```
Edit control plane, this master node has taint no schedule, **thats the reason master node will not have pods**.  

##### Apply a Taint to GPU Nodes

First, taint the GPU Nodes to repel/force Pods that do not require GPU resources:
```
kubectl get nodes
```

```
kubectl taint nodes i-0333b24c25bf4868b hardware=gpu:NoSchedule
```

This command adds a taint with key hardware, value gpu, and effect NoSchedule to the specified Node. As a result, Pods without a matching toleration will not be scheduled on this Node.  

This mechanism has 3 Effects: 
* NoSchedule: Pods will not be scheduled onto the tainted node unless they have a matching toleration.
* PreferNoSchedule: Scheduler tries to avoid scheduling pods onto the tainted node but can do so if necessary.
* NoExecute: Existing pods on the node without matching tolerations are evicted.

Note: Taints and Tolerance will not gurantee to have pod on the same node

Lets test:
```
vi deploy.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
  labels:
    app: apacheapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: apacheapp
  template:
    metadata:
      labels:
        app: apacheapp
    spec:
      containers:
        - name: cont1
          image: httpd
```

```
kubectl create -f deploy.yml
```

```
kubectl get pods -o wide  
```
All pods are scheudled on another node.  

##### Add a Toleration to GPU-Requiring Pods

Next, add a toleration to the Pods that require GPU resources, allowing them to be scheduled on the tainted Nodes:
```
vi deploy.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
  labels:
    app: apacheapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: apacheapp
  template:
    metadata:
      labels:
        app: apacheapp
    spec:
      containers:
        - name: cont1
          image: httpd
      tolerations:
        - key: "hardware"
          operator: "Equal"
          value: "gpu"
          effect: "NoSchedule"
```

In this Pod specification, the toleration matches the taint applied to the GPU Nodes, permitting the Pod to be scheduled on those Nodes.


###### Key Points:

Taints are applied to Nodes to repel certain Pods. They consist of a key, value, and effect (NoSchedule, PreferNoSchedule, or NoExecute). ​  
Tolerations are applied to Pods to allow them to be scheduled on Nodes with matching taints. They must match the key, value, and effect of the taint to be effective.



SUMMARY
--------------

Taint should be used when you want to mark a node as unavailable for certain pods. For example, you can use taint to mark a node as "maintenance" and prevent pods from being scheduled on the node while it is undergoing maintenance.  

Node selector is a simpler and more primitive mechanism compared to node affinity and is sufficient for many use cases.  

Node affinity should be used when you want to specify which nodes a pod should or should not be scheduled on based on node labels. Node affinity provides more fine-grained control over pod scheduling compared to node selector and allows you to specify complex rules for pod scheduling based on multiple node labels.  

Pod affinity allows us to set priorities for which nodes to place our pods based off the attributes of other pods running on those nodes. This works well for grouping pods together in the same node.  

Pod anti-affinity allows us to accomplish the opposite, ensuring certain pods don’t run on the same node as other pods. We are going to use this to make sure our pods that run the same application are spread among multiple nodes. To do this, we will tell the scheduler to not place a pod with a particular label onto a node that contains a pod with the same label.  

[Use this for more knowledge on taints-and-tollerations-vs-node-affinity](https://blog.devops.dev/taints-and-tollerations-vs-node-affinity-42ec5305e11a)


## Refer 3 and 3.2 before going through this section.
### Blue Green Deployment

**Objective**: Lets say i have a deployment ( named as BLUE) and i have few updates and i want to change my deployment to new deployment (named as GREEN).  

##### Blue deployment
```
vi blue.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-blue
  labels:
    app: my-app
    version: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: blue
  template:
    metadata:
      labels:
        app: my-app
        version: blue
    spec:
      containers:
      - name: my-app
        image: nginx
        ports:
        - containerPort: 80
```

```
kubectl create -f blue.yml
```

create a service for the deployment.
```
vi service.yml
```

```
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
    version: blue
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

```
kubectl create -f blue-service.yml
```

Grab the ELB's url from following command, and access the application.
```
kubectl get service
```


##### Green deployment.
```
vi green.yml
```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-green
  labels:
    app: my-app
    version: green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: green
  template:
    metadata:
      labels:
        app: my-app
        version: green
    spec:
      containers:
      - name: my-app
        image: httpd
        ports:
        - containerPort: 80
```


```
kubectl create -f green.yml
```

#### **Lets switch**.  
The whole switch happens at the service.yml object.   
`blue.yml` uses the `version: blue` tag/label while the `green.yml` uses the `version: green`. so edit the service.yml to change the version tag/label from blue to green.  

Apply those changes.
```
kubectl apply -f service.yml
```

refresh the edl to reflect the changes.


### Cannary Deployment Project

Example of a real-world Kubernetes Canary Deployment using an NGINX web app. This shows how to gradually shift traffic from v1 (stable) to v2 (canary) — perfect for production-style rollouts.

🎯**Goal**
Deploy a new version of an app (v2) side-by-side with the old version (v1), and split traffic using a Kubernetes Service.

Create a separate namespace if required,

```
kubectl create namespace canary-demo
```

```
kubectl config set-context --current --namespace=canary-demo
```

```
kubectl get pods
```
no pods

🔹 Step 1: 
Deploy Nginx or app v1  -- if required replace nginx:1.19 to some real image ib-rs or wordpres or nextwave

```
vi nginx-v1.yml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-v1
  labels:
    app: nginx
    version: v1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
      version: v1
  template:
    metadata:
      labels:
        app: nginx
        version: v1
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
        ports:
        - containerPort: 80
```

```
kubectl apply -f nginx-v1.yml
```

🔹 Step 2: Deploy Nginx or app v2
```
vi nginx-v2.yml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-v2
  labels:
    app: nginx
    version: v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
      version: v2
  template:
    metadata:
      labels:
        app: nginx
        version: v2
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```
```
kubectl apply -f nginx-v2.yml
```
🔹 Step 3: Create Nginx Service with ELB
```
vi nginx-service.yml
```
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "classic"
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
```
kubectl apply -f nginx-service.yml
```

📌 What Are Kubernetes Annotations?
In Kubernetes, annotations are key-value pairs used to attach non-identifying metadata to objects (like Pods, Services, Deployments, etc.). Unlike labels, which are used for selection (e.g. for scheduling or selectors), annotations are meant for storing additional information that tools, controllers, or humans can use.

**🧠 Difference Between Labels and Annotations**
| Feature     | **Labels**                         | **Annotations**                                    |
| ----------- | ---------------------------------- | -------------------------------------------------- |
| Purpose     | Identifying, grouping, selecting   | Attaching extra metadata                           |
| Used by     | Kubernetes core system (selectors) | Tools, controllers, scripts                        |
| Max size    | Small (\~63 chars per key/value)   | Can be large (up to 256 KB total)                  |
| Example use | `app=nginx`, `tier=backend`        | `kubectl.kubernetes.io/last-applied-configuration` |


Get ELB URL
```
kubectl get svc nginx-service
```

http:/elbdns  

Repeat a few times — you'll randomly hit either:  
NGINX v1 (from nginx-v1 pods)  
NGINX v2 (from nginx-v2 pod)  

**📈 Canary Rollout Strategy**  
You can simulate progressive rollout by:  
* Increasing v2 replicas:
```
kubectl scale deployment nginx-v2 --replicas=2
```

* Reducing v1 replicas:
```
kubectl scale deployment nginx-v1 --replicas=1
```

This gradually shifts more traffic to v2.
```
kubectl scale deployment nginx-v1 --replicas=0
```
```
kubectl get pods
```
now has only v2 pods.



-----------------------------------------------------



# 16. HELM and ARGOCD 

### 16.1 HELM:

​Helm is a powerful package manager for Kubernetes, designed to streamline the deployment and management of applications within Kubernetes clusters. Often referred to as the "Kubernetes package manager," Helm utilizes charts—pre-configured packages of Kubernetes resources—to simplify the process of defining, installing, and upgrading even the most complex Kubernetes applications.



In K8S Helm is a package manager to install packages
in Redhat: yum & Ubuntu: apt & K8s: helm

it is used to install applications on clusters.
we can install and deploy applications by using helm
it manages k8s resources packages through charts
chart is a collection of files organized on a directory structure.
chart is collection of manifest files.
a running instance of a chart with a specific config is called a release.
The Helm client and library is written in the Go programming language.
The library uses the Kubernetes client library to communicate with Kubernetes.


ARCHITECURE:
1. HELM REPOSITORY: IT HAS ALL HELM REPOS WHICH IS PUBLICALLY AVAILABLE
2. HELM CLIENT: DOWNLOADS HELM CHARTS FORM HELM REPOS.
3. API SERVER: DOWNLOADED HELM CHARTS WILL BE EXECUTED ON CLUSTER WITH API SERVER.

### 16.2 ARGOCD 

To do deployment in K8S Cluster.  

GitOps continuous delivery tool for Kubernetes.  

It automates the deployment of applications to Kubernetes clusters,  
Pipeline is not the best method for K8s, use ARGOCD




INTRO:
ArgoCD is a declarative continuous delivery tool for Kubernetes. ArgoCD is the core component of Argo Project.  
It helps to automate the deployment and management of applications in a K8s cluster. It uses GitOps methodology to manage the application lifecycle and provides a simple and intuitive UI to monitor the application state, rollout changes, and rollbacks.  
With ArgoCD, you can define the desired state of your Kubernetes applications as YAML manifests and version control them in a Git repository.  
ArgoCD will continuously monitor the Git repository for changes and automatically apply them to the Kubernetes cluster.  
ArgoCD also provides advanced features like application health monitoring, automated drift detection, and support for multiple environments such as production, staging, and development.  
It is a popular tool among DevOps teams who want to streamline their Kubernetes application deployment process and ensure consistency and reliability in their infrastructure.  


Setup KOPS first and with the helm we will setup ARGOCD.  

#### Setup KOPS

Install HELM
-------------

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```
```
chmod 700 get_helm.sh
```
```
./get_helm.sh
```
```
helm version
```

Install ARGOCD using HELM
---------------------------
```
helm repo add argo https://argoproj.github.io/argo-helm
```
```
helm repo update
```
It will update latest charts

```
kubectl create namespace argocd
helm install argocd argo/argo-cd --namespace argocd
```
```
kubectl get all -n argocd
```

EXPOSE ARGOCD SERVER:
--------------------

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
```
yum install jq -y
```
//export ARGOCD_SERVER='kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname''

//echo $ARGOCD_SERVER

```
kubectl get svc argocd-server -n argocd -o json | jq --raw-output .status.loadBalancer.ingress[0].hostname
```
The above command will provide load balancer URL to access ARGO CD


TO GET ARGO CD PASSWORD:
------------------------
The following command to provide password to access argo cd.

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Try exporting the above command and stor it in variable.
```
export ARGO_PWD='kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d'
```
```
echo $ARGO_PWD
```


Open ArgoCD load balancer  

username: admin and password from above command  

create app --> Application Name --> bankapp --> Project Name --> default --> Sync Policy --> Automatic --> Repository --> https://github.com/ReyazShaik/ar-deploy.git --> Path --> ./ --> CLuster URL --> NameSpace --> default

```
kubectl get po
```
--> it created automatically from argocd

```
kubectl get svc  --> copy paste the elb on browser
```

now modify replica as 5 in GitHub deploy.yml , automatically argocd will deploy

History and RollBack
------

Click on history and roll back --> three dots --> rollback
