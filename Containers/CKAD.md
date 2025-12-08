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

.
