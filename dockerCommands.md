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
	
If you would like to use Docker as a non-root user, you should now consider adding your user to the “docker” group with something like:
	sudo usermod -aG docker your-user

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
