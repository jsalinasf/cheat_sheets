# KUBERNETES Administration Commands

## Basic Overview

### Node

It is a physical or virtual machine in which kubernetes components are installed

### Cluster

It is a set of Nodes grouped together  
There are two roles: Master and Worker (also known as Minion)

- Master: Responsible for orchestating containers, Manages the cluster, monitors containers/nodes, stores the information about the members of the cluster
- Worker: Runs containers

### Kubernetes Components

#### Master

1. API server: Acts as the frontend for receiving management requests for the cluster. Everyone (Users, management devices, tools, cli, etc) talks to the API server to interact with the cluster
1. etcd: It is distributed reliable key-data store used by Kubernetes where all of the data used to manage the cluster is saved. It is distributed among all of the Cluster Master Nodes. Implements locks to ensure conflicts between the masters
1. scheduler: Distributes containers amoong multiple nodes
1. controller: Responsible for noticing when containers or nodes go down. It makes decicions to bring up new containers under such a cases (Brain behind orchestation)

#### Worker

1. container runtime: It is the unbderline software that is used to run containers (Container Engine) such as: Docker, crios or rocket
1. kubelet: It is the agent that runs on each node of the cluster, it is responsible for making sure that containers are running as expected

#### PODS

1. Kubernetes does NOT deploy containers directley on the Worker Nodes, it uses PODS
1. Containers are encapsulated into a Kubernetes object called POD
1. A POD is a kubernetes object that encapsulates containers
1. PODS run on Worker Nodes
1. A POD is a SINGLE instance of an application
1. A POD is the smallest object you can create in a kubernetes cluster
1. Helper Container can be in the SAME POD as the Application POD
1. Helper Containers shares the same network space (localhost) and the same storage space
1. Helper Conainers are created and destrooyed at the same time that the application container

## Kubernetes Command Line

Kube Command Line Tool is one of the command line tools for managing Kubernetes  
It is also called KubeControl or KubeCTL  
It is used to deploy and manage applications on a Kubernetes Cluster

## Kubernetes Installation

### Prerrequistes

1. Have your nodes deployed, installed and patched (Pay attention to OS supported versions)
1. Make sure your nodes have internet access to download and install packages
1. Configure a UNIQUE hostname (update files /etc/hostname and /etc/hosts)
1. Configure a UNIQUE product_uuid (use sudo cat /sys/class/dmi/id/product_uuid to verify that UUID is different on all nodes)
1. Configure a UNIQUE MAC_Address for each node (This configuration should be updated on the hypervisor manager)
1. Configure a STATIC IP ADDRESS on the POD Network adapter

   sudo vim /etc/netplan/01-netcfg.yaml
   sudo netplan apply

1. Make sure your nodes have network connectivity between each other on the POD NETWORK
1. Enable SSH service, configure its corresponding Firewall rule and make sure the service starts automatically when the server boots

	sudo apt update
	sudo apt install openssh-server
	sudo ufw allow ssh

1. Make sure TIME is SYNC among all nodes
1. Ensure iptables tooling does not use the nftables backend (https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
1. Install curl (apt-install curl)
1. Disable SWAP, using the next commands and procedures:

	swapoff -a
	vi /etc/fstab (comment swap line using #. The line that needs to be commented is usually at the end)

### Installation

	sudo apt update
    sudo apt upgrade
    sudo apt install docker.io

Follow KUBERNETES [deployment guide](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

    sudo apt-get update && sudo apt-get install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    sudo apt-get update
    sudo apt-get install -y kubelet kubeadm kubectl
    sudo apt-mark hold kubelet kubeadm kubectl

Choose the CNI that you would like to use for your deployment: Calico, Flannel, AWS VPC, Canal, etc and MAKE SURE you get all the params required to pass to kubeadm init command. In this example I'll be using Flannel  

_Make sure --pod-network-cidr DO NOT OVERLAY with the host network CIDR_

	kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=10.1.1.21

The following set of commands need to be run because I'm not root (Refer to kubernetes for more information)

	mkdir -p $HOME/.kube
	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	sudo chown $(id -u):$(id -g) \$HOME/.kube/config

Save the information to join workers to clusters

_DO NOT UPLOAD THIS INFORMATION TO GIT or OTHER REPOS FOR PRODUCTION CLUSTERS_

    kubeadm join 10.1.1.21:6443 --token qwy2os.315shx2xggsg2694 --discovery-token-ca-cert-hash sha256:81e04e137ac12c36c41d242feea791546fe99f86d22e4a188b50ebfcce50eb44

Run the following configuration (Please refer to FLANNEL network configuration)

    sysctl net.bridge.bridge-nf-call-iptables=1

Apply FLANNEL configuration:

    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml

Once a pod network has been installed, you can confirm that it is working by checking that the CoreDNS pod is Running in the output of

    kubectl get pods --all-namespaces

And once the CoreDNS pod is up and running, you can continue by joining your nodes

Join nodes to cluster

    kubeadm join 10.1.1.21:6443 --token qwy2os.315shx2xggsg2694 --discovery-token-ca-cert-hash sha256:81e04e137ac12c36c41d242feea791546fe99f86d22e4a188b50ebfcce50eb44

## Kubernetes Commands

For precise information please refer to the official [cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

### Run an Application

It deploys an application into the cluster

	kubectl run hello-minikube
	kubectl run nginx --image=nginx

### Get Cluster information

Gets information about he cluster

	kubectl cluster-info

### Get Nodes information

List all of the nodes that are part of the cluster

	kubectl get nodes

### Get Pods information

List all of the pods that are running in the cluster

	kubectl get pods

List all of the pods that are running in the cluster with additional columns such as IP, Node, Gates
	
	kubectl get pods -o wide

To list all pods for all NameSpaces

    kubectl get pods --all-namespaces
	
To list System Pods

	kubectl get pods -n kube-system
	
To List all Pods filtered with LABELS

	kubectl get pods --selector=env=dev
	
	kubectl get pods --selector="env=prod,bu=finance,tier=frontend" (It searches for an object that matches all the labels specified)
	
To list all Keys stopred on ETCD

	kubectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only

### Get Pods EXTENDED information

List all of the pods with its detailed information (verbose mode for each pod)
	
	kubectl describe pods

### CREATE a Pod using a definition file

Run a Pod using a definition file

	kubectl create -f pod-definition.yml

### DELETE a Pod

Delete an existing Pod

	kubectl delete pods pod_name
	
* To delete all pods - USE IT CAREFULLY:

    kubectl delete --all pods

### EDIT a Running Pod

Edit definition file of a running pod and then
	
	kubectl apply -f pod-definition.yml

If the definition file is not available or if you dont want to change it, use the following command:

	kubectl edit pod pod_name
	
* Keep in mind that the last command DOES NOT UPDATE your definition file


### CREATE a ReplicaSet

    kubectl create -f replicaset-definition.yml

### TEST a YAML FILE

    kubectl create --dry-run -f rs-definition.yaml

### GET existing Replicaset

    kubectl get replicaset

### DELETE a ReplicaSet

    kubectl delete replicaset my_replicaset_name

### REPLACE a ReplicaSet

This one is used to update the current deployment of the replicaset  
First you need to UPDATE the YAML file and then you run the followingc command:

    kubectl replace -f replicaset-definition.yml

### Edit a REPLICASET when you dont have the YAML definition file

    kubectl edit replicaset new-replica-set

- Modify the replicas, save the file and the delete running container so new containers pick up the changes

### EXPORT Object definition File to YAML

Run the following command to get a YAML file of a Kubernetes object

    kubectl get replicaset my-replica-set -o yaml > my_new_defintion_file.yaml

- This trick is very useful for scenarios where you dony have the definition files of kubernetes objects

### SCALE a Replicaset

In case you don't want to modify the YAML definition file of the ReplicaSet but you only want to scale the existing pods you should run this command:

    kubectl scale --replicas=12 replicaset-definition.yml
	kubectl scale --replicas=12 rs/myreplicasetname

- It will increase the number of pods to 6 but it won't modifiy the YAMl definition file. For some changes such as changing the number of replicas, deletion of containers wont be needed

### Get All running object in Kubernetes

    kubectl get all

    * It will return the running deployments, replica sets and pods

### GET existing Deployment

    kubectl get deployments

### CREATE a DEPLOYMENT

    kubectl create -f deployment-definition.yml
	
To Register the CHANGE/CAUSE column for the kubectl rollout history use the flag "record"
	
	kubectl create -f deployment-definition.yml --record

### Describe a Deployment

    kubectl describe deploy/mydeploymentname

### DELETE a Deployment

    kubectl delete deploy/mydeploymentname

### UPDATE a Deployment

1.  Once you have finsihed updating the definition file, run the following command:

    kubectl apply -f deployment-definition.yml

1.  If the definition file is not available or in case you DO NOT WANT to update the definition file, you can use

    kubectl set image deploy/mydeploymentname mycontainername=nging=1.16-alpine-perl
	
*Pay attention, you need to use the container name in the last command

### Rollout Status Deployment

    kubetctl rollout status deployment/mydeploymentname

### History of deployed rollouts

    kubectl rollout history deployment/mydeploymentname

### UNDO a Deployment

    kubectl rollout undo deployment/mydeploymentname
	
### Managing NameSpaces

Namespaces are used as logical segmentations of a Kubernetes Cluster

NameSpaces are recommended when deployments start getting larger or when you need to isolate environments

You can assign quota of resources to each namespace
Each Namespace can have its own set of policies who define who can do what

How can a pod from one namespace refer to other pod located in a different namespace?

servicename.namespace.svc.cluster.local

servicename = Pod Name
namespace = default (Other examples: dev, prod, test)
svc
cluster.local = domain 

	Ex: mysql.connect("db-service.dev.svc.cluster.local")

By default 3 namespaces are created when installling Kubernetes:

1. Default
1. kube-system (Here you will find the resources required for the regular operation of the K8S clutser such as: DNS, Network, etc)
1. kube-public (Here you will find the resources available for all users)


### List Pods on different namespaces

	kubectl get pods --namespace=kube-system
	
### To create a resource in a different namespace

	kubectl crteate -f pod-definition.yaml --namespace=dev
	
Or it is possible to define the namespace directly on the definition file, by adding the following line:

	apiVersion: v1
	kind: Pod
	
	metadata:
	  name: myapp-pod
	  namespace: dev
	  labels:
	    app: myapp
		type: front-end
	
	spec:
	  containers:
	  -  name: nginx-container
	      image: nginx


### To create a new NameSpaces

A definition file is used:

	apiVersion: v1
	kind: Namespace
	metadata:
		name: dev
		
And then run the following command:

	kubectl create -f namespace-dev.yaml
		
Another way to create a namespace is using the following command:

	kubectl create namespace test

### SWITCHING to a different NameSpace permanently:

	kubectl config set-context $(kubectl config current-context) --namespace=dev


### TAINTS and TOLERATIONS (Scheduler - For Placing Pods on Nodes)

*Taints and Tolerations DOES NOT GUARANTEE that the pods will always run on a specific Node
*It only restricts pods from running on specific nodes
*But Pods with Tolerations may run on other nodes as well

TAINTS are for Nodes (Person)

There are 3 types of Taint-Effect:

1. NoSchedule: guaranteed that new Pods (which doesn't comply with the required Toleration) wont be placed in this nodes
1. PreferNoSchedule: Scheduler will try not to place new Pods (which doesn't comply with the required Toleration) but it is not guaranteed to happen always
1. NoExecute: existing Pods will be evicted and only Pods (which DOES comply with the required Toleration) will be placed on this node

	kubectl taint nodes node01 app=blue:NoSchedule
	
UNTaint Nodes:

	kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule-

TOLERATIONS are for PODS (Bug)

*Take a look at the TAINT configured on the master nodes

	kubectl describe node kubemaster | grep Taint

Tolerations may be added on the pod definition file, like this:

    apiVersion: v1
    kind: Pod
    metadata:
      name: mypodname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      containers:
      - name: mycontainername
    	image: nginx
	
	tolerations:
	- key: "app"
	  operator: "Equal"
	  value: "blue"
	  effect: "NoSchedule"   
	  
	  
### Node Selectors (Simpler and easier method)

It will tell the POD on which container it must run, based on Node Labels

Limitations: We can only use a SINGLE LABEL

1. Label the node

To label a Node with a specifric tag, use the following command:

	kubectl label nodes node01 size=large

1. Indicate the Pod on which container it should run

To indicate the POD on which node it should run, use the following pod definition file

    apiVersion: v1
    kind: Pod
    metadata:
      name: mypodname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      containers:
      - name: mycontainername
    	image: nginx
	
	nodeSelector:
	  size: "large"


### Node Affinity

It ensures that Pods run on particular Nodes

It extends the functionality of Node Selectors, since it can use more complex policies.

To indicate the POD on which node it should run, use the following pod definition file. The following example will allow this Pod to run either on Nodes with Large or Medium label

    apiVersion: v1
    kind: Pod
    metadata:
      name: mypodname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      containers:
      - name: mycontainername
    	image: nginx
	
	  affinity:
		nodeAffinity:
		  requiredDuringSchedulingIgnoreDuringExecution:
		    nodeSelectorTerms:
		    - matchExpressions:
		      - key: size
			    operator: In
			    values:
			    - Large
			    - Medium
				
To indicate that pods from the deployment red should run only on the master node

	apiVersion: apps/v1beta1
	kind: Deployment
	metadata:
	  labels:
		run: red
	  name: red
	spec:
	  replicas: 3
	  selector:
		matchLabels:
		  run: red
	  template:
		metadata:
		  labels:
			run: red
		spec:
		  containers:
		  - image: nginx
			name: red
		  affinity:
			nodeAffinity:
			  requiredDuringSchedulingIgnoredDuringExecution:
				nodeSelectorTerms:
				- matchExpressions:
				  - key: node-role.kubernetes.io/master
					operator: Exists

To indicate the POD should NOT run on a Node with size=smallest

    apiVersion: v1
    kind: Pod
    metadata:
      name: mypodname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      containers:
      - name: mycontainername
    	image: nginx
	
	  affinity:
		nodeAffinity:
		  requiredDuringSchedulingIgnoreDuringExecution:
		    nodeSelectorTerms:
		    - matchExpressions:
		      - key: size
			    operator: NotIn
			    values:
			    - Small
			  

There is a number of operators such as: Exists (Make sure you check the documentation for additional details)

What happens if someone changes the Label of the node while Pods are running on them, would the Pod continue to ruin on the node?  

These are the Type of Node Affinity rules:


*requiredDuringSchedulingIgnoredDuringExecution
*preferredDuringSchedulingIgnoredDuringExecution

*requiredDuringSchedulingRequiredDuringExecution
*preferredDuringSchedulingRequiredDuringExecution

Required: It is mandatory to place the Pod on a Node with the specific label. If no nodes are available, the scheduler will fail to start the node. USe this rulke when it is strictly mandatory for Pods to run on a specific Node.

Preferred:Pods would be placed on Nodes with specific label but if no nodes are available they may end up running on other nodes. Use this policy when running the Pod is more important than placing the Pod on a specific node.


### Resource Limits


*Resource Requests:

By default, Kubernetes assumes that each POD will consume around 0.5CPU and 256Mi Memory

Scheduler uses this numbers to see if the Node has enough resources available

If you know that your container will require more resources, you need to specify them in the pod definition file as such:


    apiVersion: v1
    kind: Pod
    metadata:
      name: mypodname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      containers:
      - name: mycontainername
    	image: nginx
		ports:
		- containerPort: 8080
		
		resources:
		  requests:
		    memory: "1Gi"
			cpu: 1


CPU value of "1" equals to 1 vCPU
CPU min value can go as low as 0.1 or 1m (m stands for "mili")
CPU max value can go up to the available vCPUS your node has (Ex: 4)

		
*Resource Limits:

By default, Kubernetes set the default limit of 1 vCPU and 512Mi Memory for PODS - (Remember, in Docker world there is no limits and containers can sofocate the node)

You can change these defaults by adding the fgollowing section to your definition file:

    apiVersion: v1
    kind: Pod
    metadata:
      name: mypodname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      containers:
      - name: mycontainername
    	image: nginx
		ports:
		- containerPort: 8080
		
		resources:
		  requests:
		    memory: "1Gi"
			cpu: 1
		
		limits:
			memory: "2Gi"
			cpu: 2
			
*Memory limit is not a hard limit. If a POD tries to constantly overpass this limit, it will be terminated
*State OOMKilled means that the pod run out of memory = OOMKilled means Out of Memory Killed

### DAEMON SETS

DaemonSets make sure there is always one POD in every node of the cluster

It is recommended to use for deploying apps that make the use of "agents". Instead of installing an agent on the Node, you deploy it as a Pod.

A few examples of apps that could be deployed as DaemonSets:

1. Logging
1. Monitoring
1. Network Applicances
1. kube-proxy

The DaemonSet definition File is pretty similar to the ReplicationSet one, the only difference is the Kind:

    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: myreplicasetname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      replicas: 2
      selector:
    	matchLabels:
    	  tier: frontend
      template:
    	metadata:
    	  labels:
    		app: guestbook
    		tier: frontend
    	spec:
    	  containers:
    	  - name: mycontainername
    		image: nginx


To list all DaemonSets in all namespaces:

	kubectl get daemonsets --all-namespaces
	
To describe a particular DaemonSet in a particular namespace:

	kubectl describe ds weave-net --namespace=kube-system
	
Here is an example of a definition file for a DaemonSet:

	apiVersion: apps/v1
	kind: DaemonSet
	metadata:
	  name: elasticsearch
	  namespace: kube-system
	spec:
	  selector:
		matchLabels:
		  app: fluentd-elasticsearch
	  template:
	    name: fluentd-elasticsearch-pod
		metadata:		  
		  labels:
			app: fluentd-elasticsearch
		spec:
		  containers:
		  - name: fluent-elasticsearch-container
			image: k8s.gcr.io/fluentd-elasticsearch:1.20
			
### STATIC Pods

Static Pods may be used by Kubelet (Agent insatlled on nodes) when no Kubernetes cluster is available

Kubelet can only create PODS. It can NOT create ReplicaSets, DaemonSets, Deploytments, etc.

In order for Kubelet to create new Pods, you should place the pod-definition-file on the following directory:

	/etc/Kubernetes/manifests
	
This path is declared on the Kubelet configuration file: kubelet.service

The option you should look for is:

	--pod-manifest-path=/etc/Kubernetes/manifests
	
Or, you can inspect the file (kubelet.service) and search for the advanced configuration file option which is:

	--config=kubeconfig.yaml
	
Then go to that file (kubeconfig.yaml) and search for the option: 

	staticPodPath: /etc/sdome/other/folder

Since Kubernetes cluster is not available in this scenario, to get a list of the pods you need to run the command:

	docker ps
	
*Static Pods are identified by the name of the Node at the end of the Pod Name:

In this example we have 4 Static Pods (The ones that end with master)

NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   coredns-5644d7b6d9-lzd2f         1/1     Running   0          5m56s
kube-system   coredns-5644d7b6d9-pxzk5         1/1     Running   0          5m56s
kube-system   etcd-master                      1/1     Running   0          4m58s
kube-system   kube-apiserver-master            1/1     Running   0          5m
kube-system   kube-controller-manager-master   1/1     Running   0          5m1s
kube-system   kube-proxy-76xh6                 1/1     Running   0          5m56s
kube-system   kube-proxy-mnn5f                 1/1     Running   0          5m37s
kube-system   kube-scheduler-master            1/1     Running   0          4m59s
kube-system   weave-net-jb7g5                  2/2     Running   1          5m56s
kube-system   weave-net-x9m2n                  2/2     Running   0          5m37sNAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   coredns-5644d7b6d9-lzd2f         1/1     Running   0          5m56s
kube-system   coredns-5644d7b6d9-pxzk5         1/1     Running   0          5m56s
kube-system   etcd-master                      1/1     Running   0          4m58s
kube-system   kube-apiserver-master            1/1     Running   0          5m
kube-system   kube-controller-manager-master   1/1     Running   0          5m1s
kube-system   kube-proxy-76xh6                 1/1     Running   0          5m56s
kube-system   kube-proxy-mnn5f                 1/1     Running   0          5m37s
kube-system   kube-scheduler-master            1/1     Running   0          4m59s
kube-system   weave-net-jb7g5                  2/2     Running   1          5m56s
kube-system   weave-net-x9m2n                  2/2     Running   0          5m37s
	
*Static Pods are used to deploy CONTROL PLANE components

*DaemonSDets are used to deploy monitoring Agents, Logging Agents on Nodes

*Static Pods and DaemonSets are both ignored by the Kube-Scheduler

To obtain the configuration on which a service is running:

	ps aux | grep kubelet
	

Here is an example of a defintiion file for a Static Pod:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: static-busybox
	  labels:
		tier: staticpod
	spec:
	  containers:
	  - name: static-busybox-container
		image: busybox:1.28.4
		command: ["sleep"]
		args: ["1000"]
	

### VIEW Events

	kubectl get events
	kubectl get events -o wide
	
### VIEW Logs

	kubectl logs my-custom-scheduler --name-space=kube-system

### Deploying Multiple Schedulers

Custom Schedulers must have their OWN Name

You can use the same image that Kubernetes Standar scheduler uses or you can build your own

Here is the POD DEFINITION FILE for the SCHEDULER POD:

	apiVersion: v1
	kind: Pod
	metadata:
	  annotations:
		scheduler.alpha.kubernetes.io/critical-pod: ""
	  creationTimestamp: null
	  labels:
		component: my-scheduler
		tier: control-plane
	  name: my-scheduler
	  namespace: kube-system
	spec:
	  containers:
	  - command:
		- kube-scheduler
		- --address=127.0.0.1
		- --kubeconfig=/etc/kubernetes/scheduler.conf
		- --leader-elect=false
		- --port=10282
		- --scheduler-name=my-scheduler
		- --secure-port=0
		image: k8s.gcr.io/kube-scheduler-amd64:v1.16.0
		imagePullPolicy: IfNotPresent
		livenessProbe:
		  failureThreshold: 8
		  httpGet:
			host: 127.0.0.1
			path: /healthz
			port: 10282
			scheme: HTTP
		  initialDelaySeconds: 15
		  timeoutSeconds: 15
		name: kube-scheduler
		resources:
		  requests:
			cpu: 100m
		volumeMounts:
		- mountPath: /etc/kubernetes/scheduler.conf
		  name: kubeconfig
		  readOnly: true
	  hostNetwork: true
	  priorityClassName: system-cluster-critical
	  volumes:
	  - hostPath:
		  path: /etc/kubernetes/scheduler.conf
		  type: FileOrCreate
		name: kubeconfig

Pod definition file using custom scheduler:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: annotation-default-scheduler
	  labels:
		name: multischeduler-example
	spec:
	  schedulerName: default-scheduler
	  containers:
	  - name: pod-with-default-annotation-container
		image: k8s.gcr.io/pause:2.0



## Kubernetes YAML Files Templates

### POD

These are the root level fields for the YAML file used to provision Pods  
There are REQUIRED fields, so you MUST have them on your configuration file

Here is the file pod-definition.yml

    apiVersion:
    kind:
    metadata:


    spec:v1

- apiVersion: It is the API Kubernetes version we are using to create the object. Possible values are: v1, apps/v1, etc.
- kind: It is the type of object we are trying to create: POD, Service, ReplicaSet, Deployment, etc.
- metadata: Here you will find information such as:

    metadata:
		name: pod_name (It is a string)
		labels: (It is a dictionary. It can have any value key as I wish. It would allow filtering for further operations)
			app: myapp
			type: front-end

* spec: Provides additional information for the type of object we are creating (refer to kind). It is goind to be different for different objects. It is a dictionary.
  spec: (It is a dictionary)
  containers: (It is a list/array) - name: nginx (The dash indicates that this is the first item on the list)
  image: nginx

Here it is a complete example of the file:

    apiVersion: v1
    kind: Pod
    metadata:
      name: mypodname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      containers:
      - name: mycontainername
    	image: nginx
		
		
#### Creating a POD specifying a nodeName
	apiVersion: v1
	kind: Pod
	metadata:
	  name: nginx
	spec:
	  nodeName: foo-node # schedule pod to specific node
	  containers:
	  - name: nginx
		image: nginx
		imagePullPolicy: IfNotPresent

### REPLICASET

    apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
      name: myreplicasetname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      replicas: 2
      selector:
    	matchLabels:
    	  tier: frontend
      template:
    	metadata:
    	  labels:
    		app: guestbook
    		tier: frontend
    	spec:
    	  containers:
    	  - name: mycontainername
    		image: nginx

### DEPLOYMENT

    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: myreplicasetname
      labels:
    	app: guestbook
    	tier: frontend
    spec:
      replicas: 2
      selector:
    	matchLabels:
    	  tier: frontend
      template:
    	metadata:
    	  labels:
    		app: guestbook
    		tier: frontend
    	spec:
    	  containers:
    	  - name: mycontainername
    		image: nginx

### SERVICE (NODE PORT)

apiVersion: v1
kind: Service
metadata:
  name: mycoolapp-service
spec:
  selector:
    app: guestbook
    tier: frontend
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30080
	  
### SERVICE (CLUSTER IP)

apiVersion: v1
kind: Service
metadata:
  name: mycoolapp-service
spec:
  selector:
    app: guestbook
    tier: frontend
  type: NodePort
  ports:
    - targetPort: 80
      port: 80



### NAMESPACE

apiVersion: v1
kind: Namespace
metadata:
	name: dev

### RESOURCE QUOTA (For NameSpaces)

apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
 
spec:
  hard:
    pods: "10"
	requests.cpu: "4"
	requests.memory: 5Gi
	limits.cpu: "10"
	limits.memory: 10Gi
	
## CERTIFICATION TIPS

While you would be working mostly the declarative way - using definition files, imperative commands can help in getting one time tasks done quickly, as well as generate a definition template easily. This would help save considerable amount of time during your exams.  

Before we begin, familiarize with the two options that can come in handy while working with the below commands:  

#### --dry-run

By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the --dry-run option. This will not create the resource, instead, tell you weather the resource can be created and if your command is right.

#### -o yaml
	
This will output the resource definition in YAML format on screen.  

Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.

#### Pod

	 kubectl run --generator=run-pod/v1 nginx-pod --image=nginx:alpine
	 
	 kubectl run --generator=run-pod/v1 redis --image=redis:alpine --labels="tier=db,env=prod"
	 


#### Deployment

Create a deployment

	kubectl create deployment --image=nginx nginx
	
	kubectl run webapp --image=kodekloud/webapp-color --replicas=3
	
	kubectl run blue --image=nginx --replicas=6


Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

	kubectl create deployment --image=nginx nginx --dry-run -o yaml


Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)

	kubectl run --generator=deployment/apps.v1 nginx --image=nginx --dry-run --replicas=4 -o yaml


The usage --generator=deployment/apps.v1 is deprecated as of Kubernetes 1.16. The recommended way is to use the kubectl create option instead.


IMPORTANT:

kubectl create deployment does not have a --replicas option. You could first create it and then scale it using the kubectl scale command.


Save it to a file - (If you need to modify or add some other details)

	kubectl run --generator=deployment/apps.v1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml


OR
	
	kubectl create deployment --image=nginx nginx --dry-run -o yaml > nginx-deployment.yaml

You can then update the YAML file with the replicas or any other field before creating the deployment.


#### Service

Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379

	kubectl expose pod redis --port=6379 --name redis-service --dry-run -o yaml

(This will automatically use the pod's labels as selectors)

Or

	kubectl create service clusterip redis --tcp=6379:6379 --dry-run -o yaml (This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)


Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:

	kubectl expose pod nginx --port=80 --name=nginx-service --dry-run -o yaml
	
	kubectl expose deployment nginx --port=80 --target-port=8000

(This wll automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

	kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

Example:

	kubectl expose deployment webapp --port=8080 --target-port=8080 --name=webapp-service --dry-run -o yaml > service-definition-1.yaml
	
And then add the lines for:

1. type: NodePort
1. nodePort: 30082

The final file should look like this:

	apiVersion: v1
	kind: Service
	metadata:
	  creationTimestamp: null
	  labels:
		run: webapp
	  name: webapp-service
	spec:
	  type: NodePort
	  ports:
	  - port: 8080
		protocol: TCP
		targetPort: 8080
		nodePort: 30082
	  selector:
		run: webapp
	status:
	  loadBalancer: {}


Reference:

https://kubernetes.io/docs/reference/kubectl/conventions/

