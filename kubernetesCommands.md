# KUBERNETES Administration Commands

## Basic Overview

### Node
It is a physical or virtual machine in which kubernetes components are installed

### Cluster
It is a set of Nodes grouped together  
There are two roles: Master and Worker (also known as Minion)
* Master: Responsible for orchestating containers, Manages the cluster, monitors containers/nodes, stores the information about the members of the cluster
* Worker: Runs containers

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
1. Containers are encapsulated into a Kubernetes Object called PODS
1. It is a kubernetes object that encapsulates containers
1. It is the smallest unit you can create in a kubernetes cluster
1. PODS run on Worker Nodes



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
1. Make sure TIME is SYNC among all nodes
1. Ensure iptables tooling does not use the nftables backend (https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
1. Install curl (apt-install curl)
1. Disable SWAP, using the next commands and procedures:

	swapoff -a
	vi /etc/fstab (comment swap line using #. The line that needs to be commented is usually at the end)

### Installation

Choose the CNI that you would like to use for your deployment: Calico, Flannel, AWS VPC, Canal, etc and MAKE SURE you get all the params required to pass to kubeadm init command. In this example I'll be using Flannel  

*Make sure --pod-network-cidr DO NOT OVERLAY with the host network CIDR*
	
	kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=10.1.1.21

The following set of commands need to be run because I'm not root (Refer to kubernetes for more information)
	
	mkdir -p $HOME/.kube
	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	sudo chown $(id -u):$(id -g) $HOME/.kube/config
	
Save the information to join workers to clusters  

*DO NOT UPLOAD THIS INFORMATION TO GIT or OTHER REPOS FOR PRODUCTION CLUSTERS*

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

### Running an Application
It deploys an application into the cluster  
	
	kubectl run hello-minikube

### Get Cluster information
Gets information about he cluster  
	
	kubectl cluster-info

### Get Nodes information
List all of the nodes that are part of the cluster  
	
	kubectl get nodes






