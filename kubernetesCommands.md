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


## Kubernetes Command Line
Kube Command Line Tool is one of the command line tools for managing Kubernetes  
It is also called KubeControl or KubeCTL  
It is used to deploy and manage applications on a Kubernetes Cluster

## Kubernetes Installation

1. Have your nodes deployed, installed and patched (Pay attention to OS supported versions)
1. Make sure your nodes have internet access to download and install packages
1. Configure a UNIQUE hostname, UNIQUE product_uuid and a UNIQUE MAC Address for each node
1. Each server should have a static IP for the POD Network
1. Make sure your servers have network connectivity between each other
1. For Linux Boxes: Enable SSH service, configure its corresponding Firewall rule and make sure the service starts automatically when the server boots
1. Disable SWAP, using the next commands and procedures:

	swapoff -a
	vi /etc/fstab (comment swap line using #. The line that needs to be commented is usually at the end)
	


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






