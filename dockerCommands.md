### Inetersting Links:
#### Official Documentaion
https://docs.docker.com

#### Images Repo
https://hub.docker.com

### Install Docker
Follow installation guide for your operative system flavor  
Recommendation: Use the convinient script (found in the installation guide website)

	$ curl -fsSL https://get.docker.com -o get-docker.sh
	$ sudo sh get-docker.sh
	
If you would like to use Docker as a non-root user, you should now consider adding your user to the “docker” group with something like:
	$ sudo usermod -aG docker your-user

### Docker Version
	$ sudo docker version

### Run Imagen
	$ sudo docker run "image_name"
	$ sudo docker run docker/whalesay cowsay Hello-world!
