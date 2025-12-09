# Understanding and Using Containers

## What is a Container
* Containers provide a method to run application in full isolation . Such as linux systemd process
    - To provide this isolation, Linux kernel namespaces are used.
    - Container images typically are obtained from a registry
    - To provide all dependencies for running the application, a container is started from an image.


* A container runtime is the program that starts a container
    - runc is a common container runtime
    - To manage a container that runs on a stand-alone computer, a container enginee is used on top of the container runtime.
    - Docker and Podman are the most common container engines for stand-alone containers.
    - To run containers in the cloud, Kubernetes is used as the container engine
    - As Kubernetes is doing much more than just taking care of the running container, it is called a container orchestrator.


## Registries
- A registry is used as a container image store.
    - To run a container images from specific registries, a Fully Qualified Container (image) Name (FQCN)

* To start the container, you need to refer to the image
    Exact way to run:
    docker run nginx : For Docker
    podman run docker.io/library/nginx : Podman 
    kubectl run mynginx --image=docker.io/library/nginx : kubernetes


docker run : start container immediately
docker ps : show running container
docker ps -a : shows all containers that have been run in the past
docker run -d <container_name> : Run the containers in detech mode
docker run run -it <container_name> allows you to run the container and log into it.

## Managing Containers
docker images : list of container images
docker inspect : used to show properties of currently containers

To remove a container: docker rm <container_name>

docker kill is used to kill an unresponsive container 

docker logs <container_name>

# Managing Container Images 

- Container Images Architectures
    - Inside the Dockerfile, where the FROM statement is used to refer to the system image that is used
    - Container images are layerd on top of each other.

* Before starting a container, required images are pulled and stored
    - Images can be pre-fetched using docker pull command
    - docker images for a list of currently stored images
* By default image policy may automatically pulled if available.
    - The default image pull policy in Docker is always
    - In Kubernetes, it can be set to newer.
    - To remove unused images, use docker image prune.

## Image Creation Options

docker commit allows you to create a customer image by saving changes made to a running container
    - Dockerfile AKA Containerfile (Redhat) builds a customer image based on components defineed in the file.
    - buildah build custom images in a scripted way

Common Base images are:
    - Busybox
    - Alpine
    - Red Hat UBI (Universal Base Image)

# Using Dockerfile to Build Custom Image

* To build an image from a Dockerfile, use docker build -t imageine .
    - . refers to the directory where your "Dockerfile" is located. Alternatively you can specify the directory location
    - -t (tag) specifies the name of the image you want to create

When creating a script in dockerfile use #!/bin/sh

To build an Dockerfile

cd ckad/dockerfile
cat Dockerfile
docker build -t myapp .
docker images
docker image inspect myapp
docker run my app
docker run my app 3

# Creating Image From a Running Container
Steps to Create Image with docker commit
- docker run --name customweb -it nginx sh : logs you in the shell of the container
    - touch /tmp/testfile
    - exit

- docker commit customweb nginx:custome
- docker images
- docker run -it nginx:custom ls -l /tmp/testfile


* Creating Custom Image

linux@server1:~/ckad$ docker images 
                                                            i Info →   U  In Use
IMAGE          ID             DISK USAGE   CONTENT SIZE   EXTRA
myapp:latest   bc73ef3f5fb7       12.6MB         3.86MB    U   
nginx:latest   553f64aecdc3        225MB         62.6MB    U   
linux@server1:~/ckad$ docker run --name customweb -it nginx sh
# echo "welcome" >> /tmp/testfile
# exit
linux@server1:~/ckad$ docker commit customweb nginx:custom
sha256:4a0653f8b94f54a0debf4923b569ff3750c2a1300d8965ec92d6ad2f7b8d59d7
linux@server1:~/ckad$ docker images
                                                            i Info →   U  In Use
IMAGE          ID             DISK USAGE   CONTENT SIZE   EXTRA
myapp:latest   bc73ef3f5fb7       12.6MB         3.86MB    U   
nginx:custom   4a0653f8b94f        222MB         59.8MB        
nginx:latest   553f64aecdc3        225MB         62.6MB    U   
linux@server1:~/ckad$ docker run -it nginx:custom ls -l /tmp/testfile
-rw-r--r--. 1 root root 8 Dec  9 20:55 /tmp/testfile
linux@server1:~/ckad$ docker run -it nginx:custom cat /tmp/testfile
welcome
linux@server1:~/ckad$ 

# Understanding Kubernetes

What is Cloud Native 
- To provide access to applications on the Internet, the applications should be 
hosted in cloud

Kubernetes open source platform that allows containers to be used in
in a cloud native environment
    - Kubernetes API defines a set of resources types such as Pods,
    Deployment, and ConfigMap that allows for storing information in the cloud
    native environment where no relation to specific servers exists.

## Kubernetes Architecture

* The control plane consists of one or more nodes where Kubernetes
core services are running
    - Kube-apiserver: provides access to the API
    - etcd: the Kubernetes database
    - kube-scheduler : responsible for scheduling Pods at specific location
    - kube-controller-manager: manages core Kubernetes process
* The worker nodes run the containerized applications by using two core services
    - container runtime: the part that actually runs the container
    - kubelete: the part that is contacted by kube-scheduler to run the actual containers in Pods

