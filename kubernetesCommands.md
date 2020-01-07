# KUBERNETES Administration Commands

## Basic Overview of Kubernetes Components

### NODE
It is a physical or virtual machine in which kubernetes is installed

### CLUSTER
It is a set of Nodes grouped together
There are two roles: Master and Worker (also known as Minion)
* Master: Manages the cluster, monitors containers/nodes, stores the information about the members of the cluster
* Worker (Minion): Runs containers

### KUBERNETES COMPONENTS

#### MASTER
1. api/server: Receives configuration/management requests from frontend tools, cli
1. etcd: stores cluster configuration. It is a key:value shared store. It is distrinbuted between all of the MASTERS
1. scheduler

#### WORKER
1. runtime (container engine: docker, crios or rocket)
1. kubelet (kubernetes agent)






