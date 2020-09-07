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

    kubectl replace -f repliwebapp-colorset-definition.yml

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
	
Another way to do it is:

	kubectl run nginx --image=nginx

### Describe a Deployment

    kubectl describe deploy/mydeploymentname

### DELETE a Deployment

    kubectl delete deploy/mydeploymentname

### UPDATE a Deployment

1.  Once you have finsihed updating the definition file, run the following command:

    kubectl apply -f deployment-definition.yml

1.  If the definition file is not available or in case you DO NOT WANT to update the definition file, you can use

    kubectl set image deploy/mydeploymentname mycontainername=nginx=1.16-alpine-perl
	
*Pay attention, you need to use the container name in the last command

Another command to update an existing deployment is:

	kubectl edit deployment frontend

Modify the required fields.

In case of updateiong the rollout strategy, make to RECREATE make sure you delete the properties of rollingupdate

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

You can change these defaults by adding the following section to your definition file:

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

Static Pods may be used by Kubelet (Agent installed on nodes) when no Kubernetes cluster is available

Kubelet can only create PODS. It can NOT create ReplicaSets, DaemonSets, Deploytments, etc.

In order for Kubelet to create new Pods, you should place the pod-definition-file on the following directory:

	/etc/Kubernetes/manifests
	
This path is declared on the Kubelet configuration file: kubelet.service

The option you should look for is:

	--pod-manifest-path=/etc/Kubernetes/manifests
	
Or, you can inspect the file (kubelet.service) and search for the advanced configuration file option which is:

	--config=kubeconfig.yaml
	
Then go to that file (kubeconfig.yaml) and search for the option: 

	staticPodPath: /etc/some/other/folder

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
	
### VIEW Logs and Events

	kubectl logs my-custom-scheduler -n=kube-system
	
	kubectl logs -f event-simulator-pod (-f will allow you to see the real life strem of logs)
	
if the POD contain 2 or more containers, you need to specify the name of the container otherwise it will fail

	kubectl logs -f event-simulator-pod event-simulator-container
	
#### View Events

	kubectl get events

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



## Logging and Monitoring

Monitoring Tools:

*Metrics Server (previously called heapster)
*Prometheus
*Elastic Stack
*DataGog
*dynatrace

Metrics Server Deployment:

[Kubernetes metrics-server Installation:](https://medium.com/@cagri.ersen/kubernetes-metrics-server-installation-d93380de008)

	git clone https://github.com/kubernetes-incubator/metrics-server.git
	cd metrics-server
	kubectl apply -f deploy/kubernetes/
	
	
To see if everything went well:

	kubectl get apiservices |egrep metrics
	
	kubectl get deploy,svc -n kube-system |egrep metrics-server
	
	kubectl top node
	
	kubectl top pod
	
	kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes" |jq   (|jq is to return json format)
	
If errors show up, try the following:

Troubleshooting

If kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes" command returns empty response and metrics-server pod throw an error like


	E0903  1 manager.go:102] unable to fully collect metrics: [unable to fully scrape metrics from source kubelet_summary:<hostname>: unable to fetch metrics from Kubelet <hostname> (<hostname>): Get https://<hostname>:10250/stats/summary/: dial tcp: lookup <hostname> on 10.96.0.10:53: no such host]

This error is related to a known issue as of metrics-server v0.3.1 which reported [here](https://github.com/kubernetes-incubator/metrics-server/issues/131)

To fix the issue, you need to edit metrics-server-deployment.yaml and add the parameters below right after image: k8s.gcr.io/metrics-server-amd64:v0.3.1 line:

	command:
	  - /metrics-server
      - --kubelet-insecure-tls
      - --kubelet-preferred-address-types=InternalIP

then re-apply it as $ kubectl apply -f metrics-server-deployment.yaml



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


#### Creating a POD specifying a COMMAND AND PARAMETER (Argument)
	apiVersion: v1
	kind: Pod
	metadata:
	  name: ubuntu-sleeper-pod
	spec:	  
	  containers:
	  - name: ubuntu-sleeper-container
		image: ubuntu-sleeper
		command: ["sleep 2.0"]  # It overrides the ENTRYPOINT instruction of a docker file
		args: ["10"]  # It overrides the CMD instruction of a docker file


#### Creating a POD specifying ENVIRONMENT VARIABLES
	apiVersion: v1
	kind: Pod
	metadata:
	  name: ubuntu-sleeper-pod
	spec:	  
	  containers:
	  - name: ubuntu-sleeper-container
		image: ubuntu-sleeper
		ports:
		  - containerPOrt: 8080
		env:
		  - name: APP_COLOR
		    value: PINK


### ConfigMap (Configuration Maps)

There are 2 ways to create a configuration Map:

1. Imperative Way (command: kubectl create config map)
1. Declarative Way (kubectl create -f .....)

**ConfigMap: Imperative Way**

	kubectl create configmap \
		app-config --from-literal=APP_COLOR=blue \
		           --from-literal=APP_MOD=prod
				   
where app-config is the name of the configmap and APP_COLOR and APP_MODE are the key/values stored in it

You can also use a file to pass to this command

	kubectl create configmap \
		app-config --from-file=myFile.properties
		

**ConfigMap: Declarative Way**

	apiVersion: v1
	kind: ConfigMap
	metadata:
	  name: app-config
	data:
	  APP_COLOR: blue
	  APP_MODE: prod

	  
	kubectl create -f config-map.yaml

** View created configmaps on kubernetes:**
	
	kubectl get configmaps
	

#### Passing ConfigMaps to PODS Definition Files:

**In here Im passing a whole configmap**

	apiVersion: v1
	kind: Pod
	metadata:
      name: webapp-color
	  namespace: default
	spec:
	  containers:
	  - name: webapp-color
		image: kodekloud/webapp-color
		envFrom:
		- configMapRef:
			name: webapp-config-map 

**In here Im passing a specific key/value from the configmap**

	apiVersion: v1
	kind: Pod
	metadata:
	  name: ubuntu-sleeper-pod
	spec:	  
	  containers:
	  - name: ubuntu-sleeper-container
		image: ubuntu-sleeper
		ports:
		  - containerPort: 8080
		env:		  
		  - name: APP_COLOR
		    valueFrom:
			  configMapKeyRef:
			    name: app-config
				key: APP_COLOR
	

**You could also inject these configmaps as volumes**

		volumes:
		- name: app-config-volume
		  configMap:
		    name: app-config



### Secrets (To store and pass sesnitive data to pods)

There are 2 ways to create a SECRET:

1. Imperative Way (command: kubectl create secret generic)
1. Declarative Way (kubectl create -f .....)

**Secret: Imperative Way**

	kubectl create secret generic \
		app-secret --from-literal=DB_HOST=mysql \
		           --from-literal=DB_USER=root \
				   --from-literal=DB_Password=password.1
				   
where app-config is the name of the SECRET and DB_HOST, DB_USER and DB_Password are the secrets stored in it

You can also use a file to pass to this command

	kubectl create configmap \
		app-config --from-file=app_secret.properties


**Secret: Declarative Way**

	apiVersion: v1
	kind: Secret
	metadata:
	  name: app-secret
	# Data must be provided in an ENCODED FORMAT
	data: 
	  DB_HOST: bXlzXwW=
	  DB_USER: cm9vdA==
	  DB_Password: cFGGddjdu=eW

	  
	kubectl create -f app-secrets.yaml
	
**To create ENCODED FORMAT for sensitive data you can use the following coomand:**

	echo -n 'mysql' | base64
	
	// 'bXlzcWw='
	
To view the decoded fields run:

	echo -n 'bXlzcWw=' | base64 --decode
	
	// mysql


** View created configmaps on kubernetes:**
	
	# To view created secrets
	kubectl get secrets
	
	kubectl get secret app-secret -o yaml
	
	# To view further details about created secrets
	kubectl describe secrets 
	
	
#### Passing SECRETS to PODS Definition Files:

**In here Im passing a whole Secret**

	apiVersion: v1
	kind: Pod
	metadata:
      name: webapp-color
	  namespace: default
	spec:
	  containers:
	  - name: webapp-color
		image: kodekloud/webapp-color
		envFrom:
		- secretRef:
			name: app-secret 

**In here Im passing a specific key/value from the configmap**

	apiVersion: v1
	kind: Pod
	metadata:
	  name: ubuntu-sleeper-pod
	spec:	  
	  containers:
	  - name: ubuntu-sleeper-container
		image: ubuntu-sleeper
		ports:
		  - containerPort: 8080
		env:		  
		  - name: DB_Password
		    valueFrom:
			  secretKeyRef:
			    name: app-secret
				key: DB_Password
	

**You could also inject these configmaps as volumes**

		volumes:
		- name: app-secret-volume
		  secret:
		    secretName: app-secret




### MULTI CONTAINER Pods

In a multi-container pod, each container is expected to run a process that stays alive as long as the POD's lifecycle. For example in the multi-container pod that we talked about earlier that has a web application and logging agent, both the containers are expected to stay alive at all times. The process running in the log agent container is expected to stay alive as long as the web application is running. If any of them fails, the POD restarts.

But at times you may want to run a process that runs to completion in a container. For example a process that pulls a code or binary from a repository that will be used by the main web application. That is a task that will be run only one time when the pod is first created. Or a process that waits for an external service or database to be up before the actual application starts. That's where initContainers comes in.

An initContainer is configured in a pod like all other containers, except that it is specified inside a initContainers section, like this:

	apiVersion: v1
	kind: Pod
	metadata:
	  name: myapp-pod
	  labels:
		app: myapp
	spec:
	  containers:
	  - name: myapp-container
		image: busybox:1.28
		command: ['sh', '-c', 'echo The app is running! && sleep 3600']
	  initContainers:
	  - name: init-myservice
		image: busybox
		command: ['sh', '-c', 'git clone <some-repository-that-will-be-used-by-application> ; done;']


When a POD is first created the initContainer is run, and the process in the initContainer must run to a completion before the real container hosting the application starts. 

You can configure multiple such initContainers as well, like how we did for multi-pod containers. In that case each init container is run one at a time in sequential order.

If any of the initContainers fail to complete, Kubernetes restarts the Pod repeatedly until the Init Container succeeds.

	apiVersion: v1
	kind: Pod
	metadata:
	  name: myapp-pod
	  labels:
		app: myapp
	spec:
	  containers:
	  - name: myapp-container
		image: busybox:1.28
		command: ['sh', '-c', 'echo The app is running! && sleep 3600']
	  initContainers:
	  - name: init-myservice
		image: busybox:1.28
		command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
	  - name: init-mydb
		image: busybox:1.28
		command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']



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
	
## CLUSTER MANAGEMENT

### Cluster MAINTENANCE

To put a node on maintenance mode:

	kubectl drain node-1
	
	kubectl drain node-1 --force
	
	kubectl drain node01 --ignore-daemonsets
	
	kubectl drain node01 --ignore-daemonsets --force
	
If a POD is not part of a ReplicaSet, DaemonSet, Deployment, ReplicationController or Stateful Set, it may be lost forever
	
This command will terminate the pods that are running on node-1 gracefully. 

The pods that were running on this node-1 will be automatically launched on any other avaialble nodes

When node-1 gets back online, the pods WONT be automatically recreated on node-1
	

To bring the node back to the cluster after all the maintenance taks have finished:

	kubectl uncordon node-1


If I ever need to place a node in an state where no more PODS are scheduled, I can run the following command:

	kubectl cordon node-2
	
### Kubernetes Supported Configurations to consider before upgrading

kube-apiserver is the core component of a kubernetes cluster and as such none of the other components can be on a higher version. The only exception to this rule is kubectl

So we have that if:

kube-apiserver is on version v1.10 then

controller-manager and kube-schedular can be on version v1.9 or v1.10

kubelet and kube-proxy can be either on version v1.8, v1.9 or v1.10

Meanwhile, kubectl could be on version v1.11, v1.10 or v1.9

**Summary:**

kube-apiserver is the point of reference for all of the others versions (n)

1. controller-manager and kube-scheduler: same or n-1 version

1. kubelet and kube-proxy: same or n-2 version

1. kubectl: n+1, n or n-1 versions

### Supported Versions by Vendor

Kubernetes only provides support for the following versions:

n: current latest release  

n-1

n-2

If you happen to be on version n-3, you are not running an official supported version. Keep this in mind when you are the administrator of a kubernetes cluster

### Upgrade Process

#### Step #1

Upgrade your master nodes

Worker nodes and pods/replica-set/deployments/daemonsets, etc will continue running as usual  

Users wont experience downtime when connecting to their apps

If a container crashes while the master node is down, the scheduler service wont be available to deploy a new replacement

Also, none of the other operations of the kube-apiserver will be applicable

#### Step #2

Define your strategy to upgrade the Worker node components.

There are two possible options:

1. Upgrade all of the Worker nodes at once. It needs amaintenance windows since users will experience downtime
1. Upgrade Worker nodes one by one. Workloads will be moved to other nodes so users wont experience a downtime. No maintenance window is required
1. Provision new Worker nodes with upgraded version. This trategy is very feasible specially in cloud environments where you can provision new nodes easily. Remove old nodes.

#### kubeadm-upgrade

Official documentation: (https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

IMPORTANT: When upgrading, you can NOT jump versions. You need to pass from your current version to the next available one until you reach the one you need.

Example:

Origin: v1.11 - Target: v1.14

The upgrade process should go like this:

1. upgrade to v1.12
1. upgrade to v1.13
1. upgrade to v1.14


The following commands only applies to kubernetes cluster managed with the tool **kubeadm**

In order to upgrade, first run:

	kubeadm upgrade plan
	
This command will give you valuable information: 

* Current cluster version
* Current latest stable version availble
* Which components should be upgraded manually (kubelet)
* Command to upgrade cluster
* The BEFORE you UPDATE command which is to upgrade kubeadm tool itself

To update the kubeadm tool, run:

	apt-get upgrade -y kubeadm=1.12.0-00 #to upgrade kubeadm tool
	
	kubeadm upgrade apply v1.12.0 #to upgrade the cluster as such
	
The previous command only update the CONTROL PLANE.

Kubelets (nodes), need to be upgraded manually

Keep in mind that the following command:

	kubectl get nodes
	
Shows the version of the kubelet, and not the version of the kubernetes cluster components

Kubelet may or may not be present in the master nodes, it depends on your setup. 

kubeadm deploys kubelet on your master node

if you run the command kubectl get nodes and dont see the master nodes in the output of the command, probably that cluster was deployed using some other method (maybe a manual method)

To update the kubelet on your nodes, first upgrade the kubelet component of the MASTER NODES.

To upgrade Kubelet on the master node, run the following command:

	apt-get upgrade -y kubelet=1.12.0-00
	systemctl restart kubelet
	
To upgrade Kubelet on the worker node, run the following command:

	kubectl drain node-1 #To move pods outta this node and schedule them on other pods
	apt-get upgrade -y kubeadm=1.12.0-00 #to upgrade kubeadm tool
	apt-get upgrade -y kubelet=1.12.0-00 #to upgrade kubelet
	kubeadm upgrade node config --kubelet-version v1.12.0
	systemctl restart kubelet
	kubectl uncordon node-1
	
	
### Backup

There are two options to backup a Kubernetes Cluster:

1. Backup manually or automatically the Resource Configuration Files (Pods, ReplicaSets, Deploytments, Services, Secrets, etc). You can use Third Party tools such as: VELERO
1. Backup the ETCD


** DO NOT FORGET to backup the Persistent Volumes as well **


#### 1. Backup the Resource Configuration files

You can get your objects configuration files by running the following coomand:

	kubectl get all --all-namespaces -o yaml > all-deploy-services.yaml
	
After the file "all-deploy-services.yaml" has been generated, proceed to backup this file

#### 2. Backup ETCD

READ THIS: (https://github.com/mmumshad/kubernetes-the-hard-way/blob/master/practice-questions-answers/cluster-maintenance/backup-etcd/etcd-backup-and-restore.md)

1. Get etcdctl utility if it's not already present.

Reference: https://github.com/etcd-io/etcd/releases

ETCD_VER=v3.3.13

# choose either URL
GOOGLE_URL=https://storage.googleapis.com/etcd
GITHUB_URL=https://github.com/etcd-io/etcd/releases/download
DOWNLOAD_URL=${GOOGLE_URL}

rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
rm -rf /tmp/etcd-download-test && mkdir -p /tmp/etcd-download-test

curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
tar xzvf /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz -C /tmp/etcd-download-test --strip-components=1
rm -f /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz

/tmp/etcd-download-test/etcd --version
ETCDCTL_API=3 /tmp/etcd-download-test/etcdctl version

mv /tmp/etcd-download-test/etcdctl /usr/bin

2. Backup

ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
     --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
     snapshot save /tmp/snapshot-pre-boot.db

-----------------------------
Disaster Happens
-----------------------------
3. Restore ETCD Snapshot to a new folder

ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
     --name=master \
     --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
     --data-dir /var/lib/etcd-from-backup \
     --initial-cluster=master=https://127.0.0.1:2380 \
     --initial-cluster-token etcd-cluster-1 \
     --initial-advertise-peer-urls=https://127.0.0.1:2380 \
     snapshot restore /tmp/snapshot-pre-boot.db

4. Modify /etc/kubernetes/manifests/etcd.yaml

Update ETCD POD to use the new data directory and cluster token by modifying the pod definition file at /etc/kubernetes/manifests/etcd.yaml. When this file is updated, the ETCD pod is automatically re-created as thisis a static pod placed under the /etc/kubernetes/manifests directory.

Update --data-dir to use new target location

--data-dir=/var/lib/etcd-from-backup

Update new initial-cluster-token to specify new cluster

--initial-cluster-token=etcd-cluster-1

Update volumes and volume mounts to point to new path

    volumeMounts:
    - mountPath: /var/lib/etcd-from-backup
      name: etcd-data
    - mountPath: /etc/kubernetes/pki/etcd
      name: etcd-certs
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs

    Note: You don't really need to update data directory and volumeMounts.mountPath path above. You could simply just update the hostPath.path in the volumes section to point to the new directory. But if you are not working with a kubeadm deployed cluster, then you might have to update the data directory. That's why I left it as is.


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


## SECURITY

### Working with Certificates OPENSSL

**Make sure you watch the TTL/SSL lesson on DevOps prerrequisite course. Do the Lab!**

To generate a new key

	openssl genrsa -out apiserver-etcd-client.key 2048

To view a certificate:

	openssl x509 -in apiserver-etcd-client.crt -text -noout
	
**Pay attention to expiration date, issuer, CN/Subject**
	
To generate a new certificate request CSR (Certificate sign request)

	openssl req -new -key apiserver-etcd-client.key -subj "/CN=apiserver-etcd-client/O=system:masters" -out apiserver-etcd-client.csr
	
**Pay attention to use the correct key (Server)**
	
To sign a certificate (issue/approve a certificate request)

	openssl x509 -req -in apiserver-etcd-client.csr -CA ca.crt -CAkey ca.key -out apiserver-etcd-client.crt
	
**Pay attention to use the correct CAcert and CAkey (Server)**


### Kubernetes Certificates API

They are located on the Controller Manager: CSR-APPROVING and CSR-SIGNING

The kube-controller-manager.yaml has two options where it specify which CA certs is going to use to APPROIVE and SIGN certificate requests:

	--cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
	--cluster-signing-key-file=/etc/kubernetes/pki/ca.key

The process goes as follows:

User generates a key for himself (Lets use Jane as an example):

	openssl genrsa -out jane.key
	
Jane has to create a CSR (Certificate Signing Request)

	openssl req -new -key jane.key -subj "/CN=jane" -out jane.csr
	

Bob, the Administrator has to process Jane's CSR.

In order to do this, Bob creates a CSR Object (YAML Template).

Before creating the template, Jane's CSR should be 64-encoded so Bob has to use the following command:

	cat jane.csr | base64

Then, Bob has to create the Kubernetes CSR Object. 

The 64-encoded text should be placed on the "request" section of the YAML CSR Object Template

	apiVersion: certificates.k8s.io/v1beta1
	kind: CertificateSigningRequest
	metadata: 
	  name: jane
	spec:
	  groups:
	  - system:authenticated
	  usages:
	  - digital signature
	  - key encipherment
	  - server authenticated
	  request:
	    "LS0jfdksjdklfjskdljfcnsdijrrere....."

**IMPORTANT: The request should not have JUMP LINE characters '\n' so after you paste it, make sure you delete every jump line (salto de linea) of the coded text
	    

To watch PENDING Certificate Requests, use:

	kubectl get csr

To approve the request use:

	kubectl certificate approve jane
	
	# To deny a certificate request use:	
	# kubectl certificate deny jane

To get the generated certificate, use:

	kubectl get csr jane -o yaml
	
	# Pay attention to the status section
	.
	.
	.
	status:
	  certificate:
	  lud78nWnvbksEWerjSkksS
	  lksdlksderpiripocnbvWW
	  jeyxcg788273bcW......=
	  
Copy the certificate text, and decode it using:

	echo "lud78....=" | base64 --decode
	
The previous command will get you the certificate in plain format. Copy it and pass it to Jane.

Now Jane can use her cert to authenticate and access the Kubernetes cluster resources

### Kubeconfig

Default path:
	
	$HOME/.kube/config
	

Kube config file format:

	apiVersion: v1
	kind: Config
	
	clusters:
	- name: my-kube-playground
	  cluster:
	    certification-authority: /etc/kubernetes/pki/ca.crt
		server: https://my-kube-playground:6443
	
	contexts:
	- name: my-kube-admin@my-kube-playground
	  context:
	    cluster: my-kube-cluster
		user: my-kube-admin	
		namespace: finance
	
	users:
	- name: my-kube-admin
	  user:
	    client-certificate: /etc/kubernetes/pki/kube-admin.crt
		client-key: /etc/kubernetes/pki/kube-admin.key
	
To view the kube config file:

	kubectl config view

To specify a Config file located in a non default path, use:

	kubectl config view --kubeconfig=my-custom-config

To change the current context use,

	kubectl config use-context prod-user@production
	
	Run the command kubectl config --kubeconfig=/root/my-kube-config use-context research
	
**Important: You can pass the certificate information using its data instead of its path.

To do so, you first need to code the cert using:

	cat ca.crt | base64
	
The previous command will generate a coded text such as:

	LS0jfdksjdklfjskdljfcnsdijrrere
	kfljflsjflsidufnc3sudilnflicnul
	ljkdfssdlkcn344fgfe#..........
	
Replace the coded text on the Kube Config file like this:

	apiVersion: v1
	kind: Config
	
	clusters:
	- name: my-kube-playground
	  cluster:
	    certification-authority-data: "LS0jfdksjdklfjskdljfcnsdijrrerekfljflsjflsidufnc3sudilnflicnulljkdfssdlkcn344fgfe#.........."
		server: https://my-kube-playground:6443
	
	contexts:
	- name: my-kube-admin@my-kube-playground
	  context:
	    cluster: my-kube-cluster
		user: my-kube-admin	
		namespace: finance
	
	users:
	- name: my-kube-admin
	  user:
	    client-certificate: /etc/kubernetes/pki/kube-admin.crt
		client-key: /etc/kubernetes/pki/kube-admin.key

To decode a certificate use:

echo "LS0jfdksjdklfjskdljfcnsdijrrerekfljflsjflsidufnc3sudilnflicnulljkdfssdlkcn344fgfe#.........." | base64 --decode


## WORKING WITH ETCDCTL


etcdctl is a command line client for etcd.


In all our Kubernetes Hands-on labs, the ETCD key-value database is deployed as a static pod on the master. The version used is v3.

To make use of etcdctl for tasks such as back up and restore, make sure that you set the ETCDCTL_API to 3.


You can do this by exporting the variable ETCDCTL_API prior to using the etcdctl client. This can be done as follows:

	export ETCDCTL_API=3

	etcdctl version


To see all the options for a specific sub-command, make use of the -h or --help flag.


For example, if you want to take a snapshot of etcd, use:

etcdctl snapshot save -h and keep a note of the mandatory global options.

Since our ETCD database is TLS-Enabled, the following options are mandatory:

--cacert                verify certificates of TLS-enabled secure servers using this CA bundle

--cert                    identify secure client using this TLS certificate file

--endpoints=[127.0.0.1:2379] This is the default as ETCD is running on master node and exposed on localhost 2379.

--key                  identify secure client using this TLS key file


For a detailed explanation on how to make use of the etcdctl command line tool and work with the -h flags, check out the solution video for the Backup and Restore Lab.


### Role Based Authorization

#### Roles and Roles Bindings


**Roles and Role Bindings are used for NAMESPACES**

Roles and RoleBindings work with Namespaced resources only

NameSpaced Resources are: pods, replicasets, jobs, deployments, services, secrets, roles, rolebindings, configmaps, PVC

To get the list of complete Namespaced resources, run:

	kubectl api-resources --namespaced=true

To create a Role for Developers, use the following object Template: developer-role.yaml

	apiVersion: rbac.authorization.k8s.io/v1
	kind: Role
	metadata:
		name: developer
	rules:
	-   apiGroups: [""]
		resources: ["pods"]
		verbs: ["list","get","create","update","delete"]
		resourceNames: ["blue","orange"] ## This line is NOT mandatory, it will help to lockdown a more restrictive permissions on specific objects
	
	-	apiGroups: [""]
		resources: ["ConfigMap"]
		verbs: ["create"]

Once the template is ready, just run the create command:

	kubectl create -f developer-role.yaml
	
To associate Users to created Roles, you should use RoleBindings object such as: devuser-developer-binding.yaml

	apiVersion: rbac.authorization.k8s.io/v1
	kind: RoleBinding
	metadata:
		name: devuser-developer-binding
	subjects:
	-	kind: User
		name: dev-user
		apiGroup: rbac.authorization.k8s.io
	roleRef:
		kind: Role
		name: developer
		apiGroup: rbac.authorization.k8s.io
		
Once the template is ready, just run the create command:

	kubectl create -f devuser-developer-binding.yaml
	
To view RBAC objects you can use the following commands:

	kubectl get roles
	
	kubectl get rolebindings
	
	kubectl describe role role_name
	
	kubectl describe rolebindings role_binding_name
	
How can a user know his privileges?

He can run the following commands:

	kubectl auth can-i create deployments
	
	kubectl auth can-i delete nodes
	
The Administrator can impersonate user accounts to see the user's privileges, using the following commands:

Run these commands as an Administrator:

	kubectl auth can-i create deployments --as dev-user
	
	kubectl auth can-i delete nodes --as dev-user
	
We can extend the previous command to test namespaces

	kubectl auth can-i create pods --as dev-user --namespace production
	
	kubectl auth can-i delete services --as dev-user --namespace development

#### Cluster Roles and Cluster Roles Bindings

**Cluster Roles and Cluster Role Bindings are used for CLUSTER SCOPED resources**

**Cluster Roles and Cluster Role Bindings can also be used for NAMESPACED resources as well, across ALL-NAMESPACES!**

Cluster Roles and Cluter Roles Bindings work with Cluster Scoped Resources and NameSpaced resources.

Cluster Scoped Resources are: nodes, PV(persistant volumes), clusterroles, clusterrolesbindings, certificatesigningrequests, namespaces

Nodes can NOT be "Namespaced" because Nodes are Cluster Wide Resources

To get the list of complete Cluster Scoped resources, run:

	kubectl api-resources --namespaced=false

To create a Role for a Cluster Admintrator who can manages nodes, use the following object Template: cluster-admin-role.yaml

	apiVersion: rbac.authorization.k8s.io/v1
	kind: ClusterRole
	metadata:
		name: cluster-administrator
	rules:
	-   apiGroups: [""]
		resources: ["nodes"]  # To get the names of the resources use: kubectl api-resources
		verbs: ["list","get","create","delete"]
		
Once the template is ready, just run the create command:

	kubectl create -f cluster-admin-role.yaml

To associate Users to created Cluster Roles, you should use RoleBindings object such as: cluster-admin-role-binding.yaml

	apiVersion: rbac.authorization.k8s.io/v1
	kind: ClusterRoleBinding
	metadata:
		name: cluster-admin-binding
	subjects:
	-	kind: User
		name: cluster-admin
		apiGroup: rbac.authorization.k8s.io
	roleRef:
		kind: ClusterRole
		name: cluster-administrator
		apiGroup: rbac.authorization.k8s.io
		
Once the template is ready, just run the create command:

	kubectl create -f cluster-admin-role-binding.yaml

### Private Repository

An image is composed of:

registry / User Account / Image Repository

Example:

docker.io/nginx/nginx

If "registry" is missing, the defualt one will be assumed: docker.io

If "User Account" is missing, the default one will be used. The default "User Account" is the same as the image name

Other repositories addresses:

GCP Container Registry: gcr.io

When using docker, I can use the following command to login manually into a private repository:

	docker login private-registry.io
	
And thn manually, just enter the required credentiales (username/password)

To pull out an image after the login is complete, you can just use:

	docker run private-registry.io/apps/internal-app
	
When using Kubernetes, you use a SECRET to login into a private repository

To create a repository use:

	kubectl create secret docker-registry mySecret \
	--docker-server= private-registry.io \
	--docker-username = theUser \
	--docker-password= P@ssword.1 \
	--docker-email= theUSer@mail.com

And then, yopu need to use the image complete name in the pod definition fil;e such as this:

	apiVersion: v1
	kind: Pod
	metadata:
		name: nginxPod
	spec:
		containers:
		-	name: nginx
			image: private-registry.io/apps/internal-app
		imagePullSecrets:
		-	name: mySecret

		
# EXAM Personal Notes

## Get / Generate YAML files

Get YAML file from running deployment:

	kubectl get deployment web -o yaml > myDeployment.yaml
	
Generate YAML file from imperative dry-run command:
	
	kubectl create deployment three --image=nginx --dry-run=client -o yaml
	
Modifiying an existing Deployment:

	kubectl edit deployment

Generate a POD Definition file:

	kubectl run hello-world --image=shahiddev/k8s:1.0 --port=80 --dry-run -o yaml > mydeployment.yaml
	
Generate a Load Balancer Service:

	kubectl expose deployment hello-world --port=80 --type=LoadBalancer --dry-run -o yaml > myservice.yaml

## Basic Diagnoistic commands

	kubectl get componentstatuses
	
	kubectl get events # return cluster events
	
	kubectl get cluster-info	


## Client and Server versions

	kubectl version

	
## How how to install metric-server and kubernetes dashboard

For updated information, please visit:

https://github.com/kubernetes/dashboard

https://github.com/kubernetes-sigs/metrics-server


### Install metric-server

	kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
	
Right after every object has been created, edit the deployment

	kubectl edit deployment -n=kube-system metric-server
	
On the open editor, go to xargs for the container and add the following lines:

	--kubelet-insecure-tls
	
	--kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
	
The pod should be recreated automatically, otherwise delete it manually

To test if metric-server is working now, run:

	kubectl top nodes
	
	kubectl top pods
	
both of these commands should return statistics output

### Install kubernetes-dashboard

Run the following commands:

	kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml
	
Right after every object has been created, edit the clusterrolesbinding kubernetes-dashboard.

WARNING: This is an insecure deployment

	apiVersion: rbac.authorization.k8s.io/v1
	kind: ClusterRoleBinding
	metadata:
		name: kubernetes-dashboard
		namespace: kubernetes-dashboard
	roleRef:
		apiGroup: rbac.authorization.k8s.io
		kind: ClusterRole
		**name: cluster-admin**
	subjects:
		- kind: ServiceAccount
		  name: kubernetes-dashboard
		  namespace: kubernetes-dashboard

Delete all the pods from the kubernetes-dashboard namespace. The pods should be recreated automatically.

Update the kubernetes-dashboard service. Change its type to NodePort. Now you can access the service using the external IP from any node and the port given by the service configuration

Check logs on the kubernetes pod from the kubernetes-dashboard namespace. No errors should appear now

Login into the dashboard portal

Generate a token to authenticate using the kubernetes-dashboard Service Account, like this:

	kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep kubernetes-dashboard | awk '{print $1}')

### TAB Completion

Temporary:
source <(kubectl completion bash)

Permanent:
echo "source <(kubectl completion bash)" >> ${HOME}/.bashrc

### Service expose considerations:

	kubernetes expose deployment

Then you can edit the service to change its type to NodePort and make it reachable from the external nodes ip addresses

A service can not be exposed if:

	No port is assigned to containers

	No tag is assigned to deployment



### Create a Namespace

Imperative command:


	kubectl create namespace my-namespace

Create a file named "namespace.yaml" and enter the following:

	apiVersion: v1
	kind: Namespace
	metadata:
		name: prod
		labels:
			name: prod
			
And the run:

	kubectl apply -f namespace.yaml

### Create LimitRange

LimitRange are used to assign default Requests and Limits for a namespace

First you need to create a namespace, then create a LimitRange object template

To create a LimitRange, use the following template:

	apiVersion: v1
	kind: LimitRange
	metadata:
		name: low-resource-range
	spec:
		limits:
			- default:
				cpu: 1
                memory: 500Mi
              defaultRequest:
                cpu: 0.5
                memory: 100Mi
              type: Container

Once created, create the LimitRange object inside the corresponding namespace you intend to use

	kubectl --namespace=low-usage-limit create -f low-resource-range.yaml
	
**Important:** These are just default values. If deployments have specific requests and limits, the defaults WONT BE USED.

**Important 2:** If the deployment has a defined request/limit for memory but not for cpu, then the containers will grab memory requests/limits from the deployment and the cpu limit/requests will be assigned by the defaults of the LimitRange object

## Contexts

### View existing Contexts

	kubectl config get-contexts
	
### View merged kubeconfig

	kubectl config view

### Create a Context associated a specific namespace

	kubectl config set-context my-prod-context --namespace=prod --cluster=kubernetes --user=kubernetes-admin
	
### Switch context

	kubectl use-context my-prod-context
	
## NameSpaced Resurces

To view which objects in Kubernetes are namespaced or not, run the following:

	kubectl api-resources --namespaced=true

	kubectl api-resources --namespaced=false
	
## Labels

Add a new label 

	kubectl label deployments alpaca-prod "canary=false"
	
Remove a Label

	kubectl label deployments alpaca-prod "canary-"
	
Show labels

	kubectl get deployments --show-labels
	
List objects is Label is set:

	kubectl get pod -l canary
	
	kubectl get pod -l 'canary=true,!app'
	
Show Label as a column in the returned result of a command:

	kubectl get pods -L canary
	

	
Using Selector for Labels:

	kubectl get deployment --selector='app=alpaca'
	
	kubectl get deployment --selector='app=alpaca,ver=2'
	
	kubectl get pod --selector='canary in (true,false)'
	
	kubectl get pod --selector='canary notin (false)'
	
	kubectl get pod --selector='ver != 2'

	kubectl get deployment --selector='app' # returns objects where this label is set
	
	kubectl get deployment --selector='!app' # returns objects where this label is NOT set
	
### Evict Node from Cluster

	kubectl cordon node-02 --ignore-daemonsets --delete-local-data
	
### Bring back evitected Node

	kubectl uncordon node-02
	
### Delete Node

If the kube-apiserver cannot communicate with the kubelet on a node for 5 minutes
the default NodeLease will schedule the node for deletion

	kubectl delete node node-02
	
	kubeadm reset # to remove cluster specific information
	
You may also need to remove iptables information depending if you want to use this node for something else
	
### Get Node Taitns

	kubectl describe node | grep -i Taint

### ReplicaSets and Deployments

Delete a ReplicaSet without deleting the pods it manages

	kubectl delete rs rs-one --cascade=false

If an independent pod is create with labels that match the selector of the RS, it will be managed by the RS and if the replicas number is satisfied by then, the new pod will be terminated


Scale a deployment (imperative command)


	kubectl scale deployment kuard --replicas=2
	
## TIP:

If a deployment or RS is not detecting the changes, just delete one of the Pods

A change in state will causde the Deplotment/RS controllers to check the status of all Pods

## TO see Kubeletservice status

	systemctl status kubelet.service

To see the current state and configuration files used to run the kubelet binary

Paths of interest
	
	/etc/systemd/system/kubelet.service.d/10-kubeadm.conf
	
	/var/lib/kubelet/config.yaml
	
	staticPodPath is set to /etc/kubernetes/manifests/
	
## Troubleshooting Guides

	https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/
	
	