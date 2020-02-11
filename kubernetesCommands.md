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

By default 3 namespaces are created when installling Kubernetes:

1. Default
1. kube-system (Here you will find the resources required for the regular operation of the K8S clutser such as: DNS, Network, etc)
1. kube-public (Here you will find the resources available for all users)

NameSpaces are recommended when deployments star getting larger or when you need to isolate environments




## Kubernetes YAML Files Templates

### POD

These are the root level fields for the YAML file used to provision Pods  
There are REQUIRED fields, so you MUST have them on your configuration file

Here is the file pod-definition.yml

    apiVersion:
    kind:
    metadata:


    spec:

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
