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
_if you skip previous step you will have to run every command with sudo

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

### REMOVE a container permanently
	docker rm "container_name"
	
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
	




