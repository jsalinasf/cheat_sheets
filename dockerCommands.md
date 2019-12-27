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
** IMPORTANT: Containers only run while a process inside of the container is running, if the process ends or crashes, the container will exit. Remember the explanation about running the Ubuntu Image**

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

### RUN VOLUME Mapping
	docker run -v /localHost/folder/path:/container/filesystem/path mysql
* The path of the container will be mapped to the local Host folder/path
* When the container is stopped and deleted, data will remain persisten in the host folder/path

### RUN PORT Mapping
This will allow my users to reach the service running on my container using the IP of the host and one free port  
-p localHostPort:containerPort
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
To find a container´s environment variables follow these steps:
	docker inspect container_name  
Look for the **Config** session of the file  

	
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
* The previous command created a localimage *  

Finally, push your customized image to docker hub
	docker push user01/my-custom-app
* Where user01 is the name of my Docker Hub Account

You will probably need to login into docker hub first. To do so, run:
	docker login

### DOCKER History
It will allow you to see different things such as Layer Space consumption and other commands used to create the image
	docker history user01/my-custom-app

### DOCKER Image: ENTRYPOINT and Commands
1. ALWAYS Specify Entrypoint and Command in JSON Format
1. CMD will be added to ENTRYPOINT to create a complete instruction for the image

