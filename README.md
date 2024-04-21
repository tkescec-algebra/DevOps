# DevOps

## Red Hat Academy
Url: https://sso.redhat.com/
Password: N?f9Z2r4@-s&_dh

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


