# DevOps

https://docs.google.com/document/d/1_oZzMH-bWVhYSkoR_QNbnvcISj3RArEy/edit
https://docs.google.com/document/d/1-MPcdzw1Jtm8Sctjk1HRYtZT3TkWsPoeglCbaPm2MFw/edit

http://vcsa7.vua.cloud/

username: tkescec
password: Pa$$w0rd

kada se pokrene virtualka
user: student
pass: student

su -
pass: centos

For the upcoming Intro to DevOps exams use the following information to authenticate to OpenShift cluster:
Username: tkescec
Password: XV72Z38E61
Console URL: https://console-openshift-console.apps.vuacloud.vua.cloud 
API URL:  https://api.vuacloud.vua.cloud:6443 

Cluster is accessible only from your VM in vcsa7.vua.cloud environment.

Review instructions available on GitHub https://github.com/jstanesic/intro_to_devops_exam.

## Red Hat Academy
Url: https://sso.redhat.com/
Password: N?f9Z2r4@-s&_dh

## Docker Hub
user: codetome1503
pass: N#:22=&b]<W5)Vn

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
podman login docker.io
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
Create a wordpress container by using official wordpress
image(https://hub.docker.com/_/wordpress). It should be started in the background, connected
to the labnet network and it should publish port 80 from the container to port 8080 on the host.
Configure environment variables to connect to database container created in the previous step.
Wen referencing the database host use mysql(name of the previous container) as the host. 
```bash
podman run -d \
    --name wordpress \
    --network labnet \
    -p 8080:80 \
    -e WORDPRESS_DB_HOST=mysql \
    -e WORDPRESS_DB_NAME=wordpress \
    -e WORDPRESS_DB_USER=student \
    -e WORDPRESS_DB_PASSWORD=DB15secure! \
    wordpress:latest
```

In case you needed to delete containers to replace them with updated images or for any other
reason, all the files in the read write layer of the image would be lost. To persist the data bind
mounts and volumes are used.
Delete the old mysql container, and create a new one which will bind mount the container
directory /var/lib/mysql to host directory /home/student/mysql. Wait for the container to start
and examine the host directory. 
```bash
podman stop mysql
podman rm mysql

podman run -d \
    --name mysql \
    --network labnet \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=DB15secure! \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=student \
    -e MYSQL_PASSWORD=DB15secure! \
    -v /home/student/mysql:/var/lib/mysql \
    mysql:latest

podman run -d \
    --name postgres \
    --network labnet \
    -p 5432:5432 \
    -e POSTGRES_DB=wordpress \
    -e POSTGRES_USER=student \
    -e POSTGRES_PASSWORD=DB15secure! \
    -v /home/student/postgres:/var/lib/postgresql/data \
    postgres:latest
```

Recreate the previously created container, this time using volume instead of a bind mout.
```bash
podman stop mysql
podman rm mysql

podman volume create mysql_data

podman run -d \
    --name mysql \
    --network labnet \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=DB15secure! \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=student \
    -e MYSQL_PASSWORD=DB15secure! \
    -v mysql_data:/var/lib/mysql \
    mysql:latest
```

Install podman-compose package. 
```bash
sudo dnf install -y podman-compose
```


Create a compose file 'docker-compose.yml' which you can use to deploy wordpress and mysql. 
```yaml
version: '3'

services:
  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: student
      WORDPRESS_DB_PASSWORD: DB15secure!
    depends_on:
      - mysql

  mysql:
    image: mysql:latest
    environment:
      MYSQL_ROOT_PASSWORD: DB15secure!
      MYSQL_DATABASE: wordpress
      MYSQL_USER: student
      MYSQL_PASSWORD: DB15secure!
```
```bash
podman-compose up -d
podman-compose down
```

## OpenShift
```bash
oc login -u developer -p developer https://api.ocp4.example.com:6443
```

Identify the URL for the OpenShift web console.
```bash
oc whoami --show-console
```

Identify the URL for the OpenShift API.
```bash
oc whoami --show-server
```

```bash
oc new-project myapp
oc cluster-info
oc api-versions
oc get clusteroperator
oc get pod
oc get node
oc get all
oc create -f pod.yaml
oc delete pod quotes-ui
oc get clusteroperators dns -o yaml
```

- A container is an encapsulated process that includes the required runtime dependencies for an application to run.
- Containerization addresses the application development challenges around code portability, to aid in consistently running an application from diverse environments.
- Containerization also aims to modularize applications to improve development and maintenance on the various components of the application.
- When running containers at scale, it becomes challenging to configure and deliver high availability applications and to set up networking without a container platform, such as Kubernetes.
- Pods are the smallest organizational unit for a containerized application in a Kubernetes cluster.
- Red Hat OpenShift Container Platform (RHOCP) adds enterprise-class functions to the Kubernetes container platform to deliver the wider business needs.
- Most administrative tasks that cluster administrators and developers perform are available through the RHOCP web console.
- Logs, metrics, alerts, terminal connections to the nodes and pods in the cluster, and many other features are available through the RHOCP web console.

## Kubernetes and OpenShift Resources
Kubernetes uses API resource objects to represent the intended state of everything in the cluster. All administrative tasks require creating, viewing, and changing the API resources. Use the oc api-resources command to view the Kubernetes resources.

```bash
oc api-resources
oc api-resources --namespaced=false
oc api-resources --api-group ''
oc explain deployments
```

## Kubernetes CLI
```bash
kubectl version --client
kubectl explain pod
