# Docker Administration Commands

### Inetersting Links:
#### Official Documentaion
https://docs.docker.com

#### Images Repo
https://hub.docker.com

### INSTALL Docker
Follow installation guide for your operative system flavor  
Recommendation: Use the convinient script (found in the installation guide website)

	curl -fsSL https://get.docker.com -o get-docker.sh
	sudo sh get-docker.sh
	
If you would like to use Docker as a non-root user, you should now consider adding your user to the “docker” group with the following command:  
	
	sudo usermod -aG docker your-user  
	
_if you decide to skip previous the last step, you will have to run every docker command with sudo_

### DOCKER Files
Docker stores its files by default in the following path:
	/var/lib/docker

Within this path you will find several folders such as:
	/var/lib/docker/containers
	/var/lib/docker/images

### VERSION of Docker
	docker version

### RUN IMAGE
	docker run "image_name"
	docker run nginx
	docker run docker/whalesay cowsay Hello-world!
	
### LIST Containers (container ID, Image, command, created, status, ports, names)
	docker ps
	docker ps -a (list running containers. It also lists previously stopped or exited containers)
	
### STOP a container
	docker stop "ID" (You can get ID using docker ps)
	docker stop "container_name" (You can get container name using docker ps)

### REMOVE a container permanently (It frees up disk space)
	docker rm "container_name"
	docker rm "container_id"
	docker rm "container_01_name" "container_02_name" "container_03_name" (Removes multiple containers at once)
	
### LIST available locally Images
	docker images

### DELETE Image
	docker rmi "image_name" 
* Remember you must ensure that no containers are running off that image before attempting remove
	
### PULL Image 
This command allows me to download the image and keep it locally so when I start a container using this image it will start inmediately without having to download it

	docker pull "image_name"

### RUN IMAGE with a process
	docker run ubuntu sleep 5
* IMPORTANT: Containers only run while a process inside of the container is running, if the process ends or crashes, the container will exit. Remember the explanation about running the Ubuntu Image*

### EXECUTE a command inside docker container
	docker exec distratec_mcclintock cat /etc/hosts

### RUN IMAGE in ATTACH / DETACH mode (container will remain running on the background)
	docker run kodekloud/simple-webapp (runs in attached mode, you will see the output of the container on your screen. Press CTRL+C to exit but it will exit the container as well)
	docker run -d kodekloud/simple-webapp (runs in detached mode)

if you want to attach later to the container run the following command:
	docker attach "container_id"

### RUN IMAGE interactively (It will log me inside the container)
	docker run -it centos bash (-i flag for running a container in interactive mode. -t flag to get access to the promp of the container)

### RUN IMAGE with a specific name
	docker run --name reverseProxy nginx
	
### RUN - tag
	docker run nginx:4.50 (It will run a container using the nginx image version 4.50)
	docker run ubuntu:disco
* If you don´t specify a TAG, docker will default to the tag **LATEST**
* Go to Docker Hub to see the available TAGS for an image

### RUN VOLUME Mapping - Volume Mounting
	docker run -v /localHost/folder/path:/container/filesystem/path mysql	
* The path of the container will be mapped to the local Host folder/path
* When the container is stopped and deleted, data will remain persistent in the host folder/path

The path used for default to store volumes is:
	var/lib/docker/volumes/

Using -v is the old way of doing things, so here is the ney way of mounting volumens:

First, we need to understand that there are two types of mounts: volumen mounts and bind mounts

1. Volumen Mounts: it mounts a volumen in the var/lib/docker/volumens directory  
1. Bind Mounts: it mounts a directory from any location on the local host such as: /data

The new way of mounting volumens is using the option --mount  
The parameters are sent using the key:value pair format

	docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql mysql

* Where source is the local path of the host and
* target is the path inside of the container

* Make sure the path already exists for bind mounting

### RUN PORT Mapping
This will allow my users to reach the service running on my container using the IP of the host and one free port  
Where -p localHostPort:containerPort  

	docker run -p 80:5000 webapp
	docker run -p 81:5000 webapp (I can have multiple instances of my container running in the same host)
	docker run -p 3306:3306 mysql
	docker run -p 3306:3306 mysql (This will give me an ERROR since Host port 3306 is alredy being used)

### DOCKER INSPECT
Returns complete container information in JSON format  
You can get ALL of the DETAILS about the container  

	docker inspect "container_id"
	docker inspect "container_name"
	
### DOCKER LOGS
Returns the logs of the container running on the background (detached mode)  

	docker logs "container_id"
	docker logs "container_name"

### DOCKER Environment Variable
	docker run -e APP_COLOR=blue simple-webapp-color  

*To find a container´s environment variables follow these steps:*

	docker inspect container_name  

*Look for the **Config** session of the file*

	
### CREATE custom IMAGE
Create Dockerfile
	FROM Ubuntu

	RUN apt-get update
	RUN apt-get install python

	RUN pip install flask
	RUN pip install flask-mysql
	
	COPY . /opt/source-code
	
	ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
	
Once created the file, run the following command:

	docker build Dockerfile -t jsalinas/my-custom-app
*The previous command created a localimage*  

Finally, push your customized image to docker hub  

	docker push user01/my-custom-app
*Where user01 is the name of my Docker Hub Account*

You will probably need to login into docker hub first. To do so, run:

	docker login

### DOCKER History
It will allow you to see different things such as Layer Space consumption and other commands used to create the image  

	docker history user01/my-custom-app

### DOCKER Image: ENTRYPOINT and Commands

How to override the default image command?  
	docker run image_name [COMMAND]
	docker run ubuntuy sleep 5

You can use this options to further cutomize your image as well  
You will add them to the Dockerfile in order to run instructions  

1. ALWAYS Specify ENTRYPOINT and COMMAND in JSON Format
1. To specify a command that the image should always run, you need to include the following line into your Dockerfile: CMD ["sleep","5"] (See example #1)
1. You can also pass an argument to the command. To do so you will need to use an ENTRYPOINT (See example #2)
1. If you need to receive parameters/arguments for the command declared in ENTRYPOINT, you can use CMD. CMD will be added to ENTRYPOINT to create a complete instruction for the image (See example #2)
1. ENTRYPOINT and CMD can have a default value (ENTRYPOINT ["sleep"] CMD ["5"]) so if no arguments are specified, the image will use this default arguments to run. (See example #3)
1. You can overwrite the defaults parameters by specifying then at the time of running the image (See example #4)

* Ex: 1
Create Dockerfile
	FROM Ubuntu
	RUN apt-get update
	RUN apt-get install python
	CMD ["sleep", "5"] (The first argument is always the executable)

* Ex: 2
Create Dockerfile
	FROM Ubuntu
	RUN apt-get update
	RUN apt-get install python
	ENTRYPOINT ["sleep"] (Defines the program that will run when the image starts running)

You will run the image lije this:

	docker run custom_image_name 10

The "10" will be passed to the ENTRYPOINT and the image will run like:
	docker run custom_image_name sleep 10

* Ex: 3
Create Dockerfile
	FROM Ubuntu
	RUN apt-get update
	RUN apt-get install python
	ENTRYPOINT ["sleep"]	
	CMD ["5"]

* Ex: 4
	docker run --entrypoint sleep2.0 ubuntu-sleeper 10 (In here Im overwritting ENTRYPOINT and CMD)
	
### DOCKER LINK
It is a command line option which can be used to link two containers together (container_name:host_name)
	docker run -d --name=vote -p 5000:80 --link redis:redis voting-app
(What it odes is creates an entry in the hosts file of the voting-app container that points to the internal ip address of the redis container)

## DOCKER COMPOSE
Here is an example of a docker compose file, remeber the file must be in YAML format

	version: '3'
	services: 
	  db:
		image: postgres
		environment:
		  - POSTGRES_PASSWORD=mysecretpassword
	  wordpress:
		image: wordpress
		ports: 
		  - "8085:80"
		links:
		  - db
## Manage DOCKER daemonm installed on a remote host
	docker -H remote_host:2375 run nginx

## CGROUPS - Control/ Tree Groups
Control how many resources a container can use
	docker run --cpus=.5 ubuntu (This container will use up to 50% of the total processing capacity of the docker host)
	docker run --memory=100m nginx (This container will use up to 100MB of Ram Memory of the docker host)

## DOCKER Network
There rae three types of networks on docker:  
*none: containers connnected to this network will run in an isolated network.They can not be reached from the outside
*bridge: It is a network that can be accessed from the outside using port mapping only. Its IP address range will be in the 172.17.x.y, 172.18.x.y range. It is the default network. All of the containers connected to this netwrok can talk to each other. Multiple containers can use the same Port when connected to this network.
*host: It is the external network of the host. Containers connected to this netwrok are reachable from the outside. Each container will have its own port.

List existing networks
	docker network ls

Inspect a specific network
	docker network inspect bridge

Run a container connected to a specific network
	docker run --net=my_network nginx
	docker run --name alpine-2 --net none alpine

Create a new Network
	docker network create --driver bridge --gateway 182.18.0.1 --subnet 182.18.0.1/24 my_network_name

Run a new container an attach it to the newly created network
	docker run -d --name mysql-db --net wp-mysql-network -e MYSQL_ROOT_PASSWORD=db_pass123 mysql

### DOCKER Get inside the container terminal
	docker exec -it webapp bash
	docker exec -it webapp sh
* You will have to choose the command properly according to the distro used on the container