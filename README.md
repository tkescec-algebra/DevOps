# DevOps

## Red Hat Academy
Url: https://sso.redhat.com/
Password: N?f9Z2r4@-s&_dh

## Docker and Podamn documentation
Url: https://docs.docker.com/
Url: https://docs.podman.io/en/latest/

## Podman

To find the latest Nginx container image on Docker Hub using podman:
```bash
podman search docker.io/nginx --list-tags
```

This will pull the latest official Nginx container image from Docker Hub using Podman.
```bash
podman pull docker.io/nginx:latest
```

List running containers on the system:
```bash
podman ps
``` 

Find more information about the container image by running:
```bash
podman inspect docker.io/nginx:latest
podman inspect nginx
```

To run the Nginx container in the background:
```bash
podman run -d -p 80:80 --name web80 nginx:latest
podman run -d -p 8080:80 --name web8080 nginx:latest
```

To stop the container:
```bash
podman stop web80
podman stop web8080
```

Find more information about the container image by running:
```bash
podman inspect web80
podman inspect web8080
```

List all the images you have downloaded:
```bash
podman images
```

Export the nginx image by using the following command:
```bash
podman save -o nginx.tar docker.io/nginx:latest
podman export web8080 -o web8080.tar
```

To load the image from the tar file:
```bash
podman load -i nginx.tar
```

To remove the container:
```bash
podman rm web80
podman rm web8080
```
Manually modifying and  then saving that as a new image
```bash
podman run -it --name web8080 nginx:latest /bin/bash

apt-get update
apt-get install -y vim
vim /usr/share/nginx/html/index.html

exit

podman commit web8080 nginx:modified

podman run -d -p 8080:80 --name web8080 nginx:modified

podman stop web8080

podman rm web8080

```

Command you can use to change the contents of the default web page of
web8080 container without terminating it.
```bash
podman exec -it web8080 /bin/bash

cd /usr/share/nginx/html
echo 'Hello, this is the new content!' > index.html

exit
```

To containerize the application found at the provided GitHub repository 
```bash
git clone https://github.com/docker/getting-started-app.git
cd getting-started-app
touch Dockerfile
podman build -t getting-started-app .
podman run -d -p 3000:3000 --name myapp getting-started-app
```
Create docker file
```Dockefile
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

Build image with new tag
```bash
podman build -t getting-started-app:0.0.1 .
```

Build image using Dockerfile
```bash
podman build -f Dockerfile -t getting-started-app:0.0.1 .
```

Inspect the image contents by using the following command:
```bash
podman inspect getting-started-app:0.0.1
```

See the difference between these two images by using the following command:
```bash
podman diff getting-started-app getting-started-app:0.0.1
```

Push the image to the Docker Hub registry:
```bash
podman login
podman push getting-started-app:0.0.1
```

Change tag of image
```bash
podman tag getting-started-app:0.0.1 getting-started-app:latest
```

Remove image
```bash
podman rmi getting-started-app
```

Remove all containers
```bash
podman rm -a

podman system prune --all --force && podman rmi --all
```

Show logs for container
```bash
podman logs myapp
```

## Networking

To list all the networks on the system:
```bash
podman network ls
```
Inspect the network configuration of the default podman network:
```bash
podman network inspect podman
```
Create a custom network which will have dns enabled.
```bash
podman network create labnet
```

Create a mysql database container by using official mysql
image(https://hub.docker.com/_/mysql). It should randomize the root password, create a
database called wordpress and a user called student with password DB15secure!. It should be
started in the background and connected to labnet network. Name the container mysql.
```bash
# Generate a random password for root
ROOT_PASSWORD=$(openssl rand -base64 12)

# Create the MySQL container
podman run -d \
    --name mysql \
    --network labnet \
    -e MYSQL_ROOT_PASSWORD=$ROOT_PASSWORD \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=student \
    -e MYSQL_PASSWORD=DB15secure! \
    mysql:latest
```




