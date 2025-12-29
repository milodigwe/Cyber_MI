# Understanding and Using Containers

https://github.com/sandervanvugt/ckad 
Github repo

CKAD Exam objectives from cncf 
https://github.com/cncf/curriculum/blob/master/CKAD_Curriculum_v1.34.pdf

Kubernetes resources
https://devopscube.com/kubernetes-tutorials-beginners/

Kubernetes Offical Documentation 
https://kubernetes.io/docs/concepts/workloads/pods/


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

* API Resources
    - Resources allow for storing and running applications in a Kubernetes environment
    - Essential resource includes  :
        - Pod : the minimal entity managed by Kubernetes. Runs containers
        - Deployment:  adds replication and zero-downtime updates to the Pods
        - ConfigMap : used to store config files and startup parameters
        - Services : load balances incoming traffic to application instances
        - Ingress/Gateway API : provides a reversed proxy for application access
        - Persistent Volumes: represent persistent non-epthemeral storage


kubectl api-resources : display reources.
kubectl explain <resource> : This will explain whats going on in a resource

*** Kubernetes Doc Page ***
https://kubernetes.io/docs/home/


# Creating Your environment
Recommended to download ubuntu server and run minikube start , To configure environement.

Steps to Install AiO Kubernetes
* From git repo ./setup-container.sh
* From git repo sudo ./setup-kubetools.sh
* sudo kubeadm init
* Set up client as instructed
   mkdir -p $HOME/.kube 
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
* set up network plugin: kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
* Verify the available namespaces:
    linux2@kubernetes:~/ckad$ kubectl get ns
NAME              STATUS   AGE
default           Active   6m16s
kube-node-lease   Active   6m15s
kube-public       Active   6m16s
kube-system       Active   6m16s
linux2@kubernetes:~/ckad$ kubectl get ns ; kubectl get namespaces

Available pods in namespace :
---
kubectl get pods -n kube-system
NAME                                      READY   STATUS    RESTARTS   AGE
calico-kube-controllers-b45f49df6-kxmpg   1/1     Running   0          2m3s
calico-node-xn2wm                         1/1     Running   0          2m3s
coredns-66bc5c9577-pc866                  1/1     Running   0          7m12s
coredns-66bc5c9577-sgznz                  1/1     Running   0          7m12s
etcd-kubernetes                           1/1     Running   0          7m20s
kube-apiserver-kubernetes                 1/1     Running   0          7m18s
kube-controller-manager-kubernetes        1/1     Running   0          7m18s
kube-proxy-82rd7                          1/1     Running   0          7m12s
kube-scheduler-kubernetes                 1/1     Running   0          7m19s

--

To run a user pod on the control node you have to edit the control file:

linux2@kubernetes:~/ckad$ kubectl run testpod --image=nginx
pod/testpod created
linux2@kubernetes:~/ckad$ kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
testpod   0/1     Pending   0          30s
linux2@kubernetes:~/ckad$ 


Now edit file, to allow pod to run . Obtain this information from below :
kubectl get nodes
NAME         STATUS   ROLES           AGE   VERSION
kubernetes   Ready    control-plane   12m   v1.34.3
linux2@kubernetes:~/ckad$ 

kubectl edit node <hostname_of_system>
Inside the file you will see something like this : line #26

spec:
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/control-plane

Remove above line in file

After removing file your container will run.

* Verify: kubectl get all
* Remove the taint from the control node : kubectl edit nodenamex


## Run First Application
Command to run first application :
    kubectl create deploy firstapp --image=nginx
Use source <kubectl completion bash> to enable tab-based command-line completion for the Linux Bash shell.
    kubectl only has a man page.
Shows the man page for kubectl for bash completion

kubectl completion -h | less

To enable bash completion for current shell

kad$ env | grep $SHELL
SHELL=/bin/bash
linux2@kubernetes:~/ckad$ kubectl completion -h | less
linux2@kubernetes:~/ckad$ source <(kubectl completion bash)

kubectl create deployment --image nginx firstapp
deployment.apps/firstapp created

kubectl get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/firstapp-847875d585-p9ql5   0/1     Pending   0          31s
pod/testpod                     1/1     Running   0          24m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   34m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/firstapp   0/1     1            0           31s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/firstapp-847875d585   1         1         0       31s


Minikube is a tool that lets you run a single-node Kubernetes cluster locally on your computer. It’s primarily used for development, testing, and learning Kubernetes without needing a full multi-node cluster.

# Pod Basic Features 

## Understanding  Pods
- Podd add Kubernetes properties to containers
    - nodeSelector allows selection of the node that runs the Pod.
    - priority allows for adding priortiy labels
    - restartPolicy tells the cluster what to do if the Pod fails
- Pods can also include volumes, which make it easy to provide storage to containerized applications.

kubectl explain pod.spec | less : Show what properties go into the container yaml file.

Standalone Pods are commonly used for testing and troubleshooting
    Ex. Like Before 
        - kubectl run podname --image=imagine_name
        - kubectl get pods for an overview
        - kubectl describe pod podname shows properties of running of currently running Pods

## Running Pods the Devops Way
- Provide the configuration is often provided as code. Use YAML files.

* Basic Properites of Kubernetes YAML File
    - apiVersion: specifies which verison of the API to use for this object
    - kind: indicates the type of object (Deployment, Pod, etc)
    - metadata: contains administrative information about the object
    - spec: contains the specifics to define exactly how the resource is used
    - status : added by the cluster for running resources
* Use kubectl explain to get more information about the basic properties

* In the containers spec, different parts are commonly used
    - name : thee name of container
    - image: the image that should be used
    - command: the commmand the container should run
    - args: arguments that are used by the command
    - env : environment variables that should be used by the container
* These are all a part of the pod.spec.containers, which can be checked with kubectl explain.

* Creating Resource from YAML Files
    - kubectl create -f resource.yaml : create resource from YAML. IF the resource already exists, it fails
    - kubectl apply -f resource.yaml : create resource from YAML. IF the resource already exists, it will update properties that need updating
    - kubectl delete -f resource.yaml : deletes the resource from YAML file.
    - kubectl replace -f resource.ymal : replaces the current resource with resource specification as the YAML file.


## Generating YAML Files

* To generate YAML files, add --dry-run=client -o yaml > my.yaml
    -Ex. kubectl run myginx --image=nginx --dry-run=client -o yaml > mynginx.yaml
        - Next create resource as such : command  :kubectl apply -f myningx.yaal

-------
linux2@kubernetes:~$ kubectl run myginx --image=nginx --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: myginx
  name: myginx
spec:
  containers:
  - image: nginx
    name: myginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
linux2@kubernetes:~$ kubectl run myginx --image=nginx --dry-run=client -o yaml > mynginx.yaml
linux2@kubernetes:~$ kubectl apply -f mynginx.yaml
pod/myginx created


linux2@kubernetes:~$ kubectl get pods
NAME                        READY   STATUS    RESTARTS      AGE
firstapp-847875d585-p9ql5   1/1     Running   1 (96m ago)   2d15h
myginx                      1/1     Running   0             14s
linux2@kubernetes:~$ 


* Mult-Container Pods
    - There are some cases where you run multiple containers in a Pod
        * Sidecar container:  a container that enchances the primary app for instance for logging
        * Ambassador container: container that represents the primary container to the outside world, such as a proxy
        * Adapter container : a container that is used to adopt the traffic or data pattern to match the traffic or data pattern in other application in the cluster

## Managing Namespaces
* To show resources in all Namespace , use kubectl get -A
--
inux2@kubernetes:~$ kubectl get pods -A
NAMESPACE     NAME                                      READY   STATUS    RESTARTS       AGE
default       firstapp-847875d585-p9ql5                 1/1     Running   1 (111m ago)   2d16h
default       myginx                                    1/1     Running   0              15m
kube-system   calico-kube-controllers-b45f49df6-kxmpg   1/1     Running   1 (111m ago)   2d16h
kube-system   calico-node-xn2wm                         1/1     Running   1 (111m ago)   2d16h
kube-system   coredns-66bc5c9577-pc866                  1/1     Running   1 (111m ago)   2d16h
kube-system   coredns-66bc5c9577-sgznz                  1/1     Running   1 (111m ago)   2d16h
kube-system   etcd-kubernetes                           1/1     Running   1 (111m ago)   2d16h
kube-system   kube-apiserver-kubernetes                 1/1     Running   1 (111m ago)   2d16h
kube-system   kube-controller-manager-kubernetes        1/1     Running   1 (111m ago)   2d16h
kube-system   kube-proxy-82rd7                          1/1     Running   1 (111m ago)   2d16h
kube-system   kube-scheduler-kubernetes                 1/1     Running   1 (111m ago)   2d16h
--
* To run resources in a specific namespace, use kubectl run -n namespace
* Use kubectl create ns nsname to create a Namespace

kubectl get ns -A
NAME              STATUS   AGE
default           Active   2d16h
kube-node-lease   Active   2d16h
kube-public       Active   2d16h
kube-system       Active   2d16h


linux2@kubernetes:~$ kubectl run secret --image=nginx -n secret
pod/secret created
linux2@kubernetes:~$ kubectl get pods -n secret
NAME     READY   STATUS    RESTARTS   AGE
secret   1/1     Running   0          13s
linux2@kubernetes:~$ 

How to troubleshoot failing kubernetes cluster, get information about Pod status the kubernetes cluster for a failing Pod.

kubectl describe pod secret -n secret
kubectl logs secret -n secret
kubectl exec -it pod
kubectl exec -it secret -n secret -- sh : log into a container
# pwd
/
# hostname
secret
# 

If the pod has multiple containers, You must specify the container name. You must specify the container name:

kubectl exec -it secret -n secret -c <container-name> -- sh

Troubleshooting
* Use kubectl get pods to check on current Pod status
* Use kubectl describe pod .. to get more information about Pod status in the Kubernetes cluster for a failing Pod
* Use kubectl logs podname to get access to Pod application output that has been logged.
* Use kubectl exec -it podname sh to open a shell on the Pod and analyze specific components.

Lab 5 : Managing Pods
1. Create a YAML file that meets the following requirements:
    - A Namespace with the name "microservice" is created
    - A pod with the name "microddb", based on the mariadb image is started in this namespace
2. Create the resource using the declarative methodology.


1. Searches for the mariadb image
docker search mariadb
NAME                                         DESCRIPTION                                     STARS     OFFICIAL
mariadb                                      MariaDB Server is a high performing open sou…   6062      [OK]
mariadb/maxscale                             MariaDB MaxScale - The world's most advanced… 

2. Pull the latst image docker pull mariadb
Using default tag: latest
latest: Pulling from library/mariadb
ba2d27f4ceca: Pull complete 
20043066d3d5: Pull complete 
75e5c9f5eeb0: Pull complete 
ee5b64c3e2f5: Pull complete 
9047f410cb7d: Pull complete 
eca7ec75082e: Pull complete 
4be15142d46e: Pull complete 
be922ac1f9ed: Pull complete 
0711efb1517f: Download complete 
c2fd0cca54b4: Download complete 
Digest: sha256:e1bcd6f85781f4a875abefb11c4166c1d79e4237c23de597bf0df81fec225b40
Status: Downloaded newer image for mariadb:latest
docker.io/library/mariadb:latest

3. Inspect the image

kubectl run microdb --image=mariadb:latest
pod/microdb created
linux2@kubernetes:~$ kubectl get pods -A
NAMESPACE     NAME                                      READY   STATUS    RESTARTS      AGE
default       firstapp-847875d585-cggqd                 1/1     Running   0             31m
default       microdb                                   0/1     Error     1 (17s ago)   26s



4. Pods Errors out, so we need to investigate why
    - Investigate the logs from the container 

kubectl logs microdb
2025-12-14 08:25:45+00:00 [Note] [Entrypoint]: Entrypoint script for MariaDB Server 1:12.1.2+maria~ubu2404 started.
2025-12-14 08:25:46+00:00 [Warn] [Entrypoint]: /sys/fs/cgroup///memory.pressure not writable, functionality unavailable to MariaDB
2025-12-14 08:25:46+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2025-12-14 08:25:46+00:00 [Note] [Entrypoint]: Entrypoint script for MariaDB Server 1:12.1.2+maria~ubu2404 started.
2025-12-14 08:25:46+00:00 [ERROR] [Entrypoint]: Database is uninitialized and password option is not specified
	You need to specify one of MARIADB_ROOT_PASSWORD, MARIADB_ROOT_PASSWORD_HASH, MARIADB_ALLOW_EMPTY_ROOT_PASSWORD and MARIADB_RANDOM_ROOT_PASSWORD


5. Shows the help page on when running a container.
kubectl run microdb --image=mariadb:latest --help

--env=[]:
	Environment variables to set in the container.

6. Run container and verified its running :

linux2@kubernetes:~$ kubectl run microdb --image=mariadb:latest --env=MARIADB_ROOT_PASSWORD=password
pod/microdb created
linux2@kubernetes:~$ kubectl get pods -A
NAMESPACE     NAME                                      READY   STATUS    RESTARTS      AGE
default       firstapp-847875d585-cggqd                 1/1     Running   0             47m
default       microdb                                   1/1     Running   0             5s


7. Now lets creates a YAML File

----
First lets verify that we specified the correct arguments

kubectl run microdb --image=mariadb:latest --env=MARIADB_ROOT_PASSWORD=password -n microservice
pod/microdb created
linux2@kubernetes:~$ kubectl get pods microservice
Error from server (NotFound): pods "microservice" not found
linux2@kubernetes:~$ kubectl get pods -A
NAMESPACE      NAME                                      READY   STATUS    RESTARTS      AGE
default        firstapp-847875d585-cggqd                 1/1     Running   0             61m
kube-system    calico-kube-controllers-b45f49df6-ln5nh   1/1     Running   2 (51m ago)   61m
kube-system    calico-node-xn2wm                         1/1     Running   3 (51m ago)   3d12h
kube-system    coredns-66bc5c9577-hl6kj                  1/1     Running   2 (51m ago)   61m
kube-system    coredns-66bc5c9577-jvs47                  1/1     Running   2 (51m ago)   61m
kube-system    etcd-kubernetes                           1/1     Running   3 (51m ago)   3d12h
kube-system    kube-apiserver-kubernetes                 1/1     Running   3 (51m ago)   3d12h
kube-system    kube-controller-manager-kubernetes        1/1     Running   3 (51m ago)   3d12h
kube-system    kube-proxy-82rd7                          1/1     Running   3 (51m ago)   3d12h
kube-system    kube-scheduler-kubernetes                 1/1     Running   4 (51m ago)   3d12h
microservice   microdb                                   1/1     Running   0             15s
linux2@kubernetes:~$ kubectl get pods -n microservice
NAME      READY   STATUS    RESTARTS   AGE
microdb   1/1     Running   0          44s
linux2@kubernetes:~$ 


-----------------------

8. Create the yaml file for us dry run and then we output it to a yaml file

kubectl run microdb --image=mariadb:latest --env=MARIADB_ROOT_PASSWORD=password -n microservice --dry-run=client -o yaml

--
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: microdb
  name: microdb
  namespace: microservice
spec:
  containers:
  - env:
    - name: MARIADB_ROOT_PASSWORD
      value: password
    image: mariadb:latest
    name: microdb
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
linux2@kubernetes:~$ kubectl run microdb --image=mariadb:latest --env=MARIADB_ROOT_PASSWORD=password -n microservice --dry-run=client -o yaml > mariadb.yaml


9. Now lets execute the command from the yaml file

kubectl apply -f mariadb.yaml 
pod/microdb created
linux2@kubernetes:~$ kubectl get pods -n microservice
NAME      READY   STATUS    RESTARTS   AGE
microdb   1/1     Running   0          7s
linux2@kubernetes:~$ 


10 . Alternatively we can create the namespace separate.

Two separate commands :

kubectl create ns microservice --dry-run=client -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: microservice
spec: {}
status: {}
linux2@kubernetes:~$ kubectl create ns microservice --dry-run=client -o yaml > mariadb2.yaml
linux2@kubernetes:~$ kubectl run microdb --image=mariadb:latest --env=MARIADB_ROOT_PASSWORD=password -n microservice --dry-run=client -o yaml >> mariadb2.yaml

Then edt the file and put --- between the sections so that we create resources separate.


kubectl apply -f mariadb2.yaml 
namespace/microservice created
pod/microdb created
linux2@kubernetes:~$ 


linux2@kubernetes:~$ kubectl get pods -n microservice
NAME      READY   STATUS    RESTARTS   AGE
microdb   1/1     Running   0          9m23s
linux2@kubernetes:~$ 


# Pod Advanced Features
* An init container is a special case of a multi-container Pod, where the init container runs to completion before the main container is started

* Starting the main container depends on the suces of the init container, if the init container fails the main container will never start

Run command inside container 

kubectl exec -it init-demo -- cat /usr/share/nginx/html/index.html
Defaulted container "nginx" out of: nginx, install (init)
<html><head></head><body><header>
<title>http://info.cern.ch</title>
</header>


* Sidecar Containers
- A sidecar container is an InitContainer that has the restartPolicy field set to Always
- It doesn occur as a specific attribute, to create a sidecar you need to create an InitContainer with the restartPolicy set to Always
- The sidecar container will be started before the main Pod is started and is typically used to repeatedly run a command


* Using Port Forwarding to Access Pods

Pods can be accessed uin multiple ways
    - Simple way expose a Pod port on the kubectl host that forwards to the Pod.
    kubectl port-forward fwnginx 8080:80
    - Port forwarding is useful for testing Pod accessbility on a specific cluster node, not to expose it to external users.

Adds extra colums 

 kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP              NODE         NOMINATED NODE   READINESS GATES
firstapp-847875d585-cggqd   1/1     Running   0          126m    172.16.192.88   kubernetes   <none>           <none>
init-demo                   1/1     Running   0          26m     172.16.192.96   kubernetes   <none>           <none>
sidecarpod                  2/2     Running   0          7m29s   172.16.192.97   kubernetes   <none>           <none>


How to port forward
----

linux2@kubernetes:~$ kubectl run fwnginx --image=nginx
pod/fwnginx created
linux2@kubernetes:~$ kubectl port-forward fwnginx 8080:80 &
[1] 91925
linux2@kubernetes:~$ Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80

linux2@kubernetes:~$ curl localhost:8080
Handling connection for 8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>


---

## RestartPolicy
* The Pod restartPolicy determines what happens if a container that is managed by a Pod crashes
* If set to the default value restartPolicy=always, the container will be restarted after a crash
* restartPolicy=always does not affect the state of the entire Pod
* If the Pod is stopped or killed, restartPolicy=always won't restart it.

To get the full yaml file of pods
kubectl get pods nginx1 -o yaml

To start the container runtime start minikube : crictl (container runtime)

minikube ssh
crictl ps 

## Jobs
- A jobs starts a Pod with the restartPolicy set to never
- To create a Pod that runs to completion, use jobs instead
- Jobs are useful for one-shot tasks, like backup, calculation, batch processing, and more
-  Use spec.ttlSecondsAfterFinished to clean up completed Jobs automatically

Job Types 

3 different Jon types can be started, which is specified by the completions and parallelism parameters
    - Non-parallel Jobs : one Pod is started, unless the Pod fails
        - completions=1
        - parallelism=1
    - Parallel Jobs with a fixed completin count: the job is complete after successfully running as many times as specified in Jobs.spec.completions
        - completions=n
        - paralleslim=m

For example, let’s say you have a script that needs to be run once a day. Instead of manually starting the script each day, or scheduling a regular cron job on a specific machine (which could fail or malfunction), you can create a Kubernetes Job to handle it automatically

Commands to create a job

kubectl create job -h | less
kubectl create job onejob --image=busybox -- date
kubectl get jobs,pods

kubectl create job mynewjob --image=busybox --dry-run=client -o yaml -- sleep 60 > batch.yaml

Added three additional parameters . Run 3 batch jobs and finish after 60 seconds.
completions: 3
  ttlSecondsAfterFinished: 60


kubectl get jobs,pods
NAME                 STATUS    COMPLETIONS   DURATION   AGE
job.batch/mynewjob   Running   1/3           94s        94s

NAME                            READY   STATUS      RESTARTS      AGE
pod/firstapp-847875d585-cggqd   1/1     Running     1 (23m ago)   11h
pod/fwnginx                     1/1     Running     1 (23m ago)   9h
pod/init-demo                   1/1     Running     1 (23m ago)   9h
pod/mynewjob-x57lq              0/1     Completed   0             94s
pod/mynewjob-xjnk7              1/1     Running     0             27s
pod/sidecarpod                  2/2     Running     2 (23m ago)   9h

Batch jobs are running.


## Cronjobs
- used to run scheduled jobs
    - To test cronjobs command: kubectl create job myjob --from=cronjob/mycronjob

Creates the cronjob 

kubectl create cronjob runme --image=busybox --schedule "*/2 * * * *" -- echo from the cluster

Then run the cronjob at runtime only once 

kubectl create job runme --from=cronjob/runme
job.batch/runme created

linux2@kubernetes:~$ kubectl get cronjobs,jobs,pods
NAME                  SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/runme   */2 * * * *   <none>     False     0        87s             4m21s

NAME                       STATUS     COMPLETIONS   DURATION   AGE
job.batch/runme            Running    0/1           6s         6s
job.batch/runme-29429002   Complete   1/1           11s        3m27s
job.batch/runme-29429004   Complete   1/1           11s        87s

NAME                            READY   STATUS              RESTARTS      AGE
pod/firstapp-847875d585-cggqd   1/1     Running             1 (43m ago)   11h
pod/fwnginx                     1/1     Running             1 (43m ago)   9h
pod/init-demo                   1/1     Running             1 (43m ago)   9h
pod/runme-29429002-gvmwd        0/1     Completed           0             3m27s
pod/runme-29429004-8z295        0/1     Completed           0             87s
pod/runme-nkmtd                 0/1     ContainerCreating   0             6s
pod/sidecarpod                  2/2     Running             2 (43m ago)   9h


Logs of container
kubectl logs runme-29429006-pbxms
from the cluster

## Cleaning Up Resources 

kubectl explain cronjob.spec
    - Based of the fields in the man page we can fliter for the specif page.
    kubectl explain cronjob.spec.jobTemplate.spec


# Kubernetes Storage
 When a container is started, the container working environment is created as a directory on the host runs the container.

 - When cloud storage is host-bound, it needs to be synchronized when replicated Pords on different nodes

 - Pod volumes are a Pod property that allow containers to connect to any storage type that is defined within the Pod
    - PersistenVolumes are independent API resources and cand be discovered dynamically while running Pods

## Configuring Pod Volume Storage
Many types of storage can be addressed using volumes see pod.spec.volumes for a list

To use a Pod volume, the container needs to mount it, using pod.spec.containers.volumeMounts

Common Pod Volune Type
- emptyDir : creates a temporary directory on the host that runs a Pod and is ephemeral
- HostPath refers to a persistent directory on the host that runs the Pod
- PersistentVolumeClaim connects to available PersistentVolumes


## Configuring Persisten Volumes ###
* A Pod volumes can use a persistent storage type
* Pods connect to PersistenVolumes using the PersistenVolume Claim API Resource
    - Search for "Create a persistenvolume" in kubernetes.io
    - PersistenVolumes, manually make sure they have storageClassName property set

kubectl api-resources | grep persistent
persistentvolumeclaims              pvc          v1                                true         PersistentVolumeClaim
persistentvolumes                   pv           v1                                false        PersistentVolume


Then search for documentation using kubectl explain
- This is the documentation that has the mandatory filed
kubectl explain PersistentVolumes.spec

kubectl get pv task-pv-volume
NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
task-pv-volume   10Gi       RWO            Retain           Available           manual         <unset>                          87s


## StorageClass
- A StorageClass in Kubernetes defines how persistent storage is dynamically provisioned in a claster
    - storage profile or blueprint that tells what kind of storage to create and how

- A PersistenVolumeClaim (PVC) in Kubernetes is a request for persistent storage made by a user or application.

Minikube is a local kubernetes cluster
    - one control plane, worker node, etc..

## Lab 7 : Setting up Storage ##
* Create a PersistentVolume based on the hotPath storage type, using the directory /storage and the ReadWriteMany storage type

* Run a Pod based on the Busybox image, with the sleep infifnity command as default command.. Configure this Pod such that it mounts the PV storage on the directory / data

* Use kubectl cp command to copy the /etc/hosts file from your computer to the PV storage.
Verify on the storage host that the storage has been written.


Step 1 :
Search for Configure Pod Persistent Storage

Copy the pv-volume.yaml yaml file (PersistenVolme)
modfiy it to add the parameters:

apiVersion: v1
kind: PersistentVolume
metadata:
  name: lab7-pv
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/storage"


** Verify pv created **

kubectl get pv -A
NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
lab7-pv          10Gi       RWX            Retain           Available           manual         <unset>                          9s


Step 2 - Now create pvc  (Persisten Volume Claim)

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: lab7pvc
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 3Gi



kubectl get pvc,pv -A
NAMESPACE   NAME                            STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
default     persistentvolumeclaim/lab7pvc   Bound    lab7-pv   10Gi       RWX            manual         <unset>                 103s

NAMESPACE   NAME                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
            persistentvolume/lab7-pv   10Gi       RWX            Retain           Bound    default/lab7pvc   manual         <unset>                          3m38s


At this step the status should say "Bound"


Step -3 : (Now we crate the Pod that will use it)

piVersion: v1
kind: Pod
metadata:
  name: lab7pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: lab7pvc
  containers:
    - name: task-pv-container
      image: busybox
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage


Step 4 : Generate a yaml file , we need to add --sleep inifity to the Pod

kubectl run dummy --image=busy --dry-run=client -o yaml -- sleep infinity
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: dummy
  name: dummy
spec:
  containers:
  - args:
    - sleep
    - infinity
    image: busy
    name: dummy
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

5. This will generate the arguments we need to add to the Pod file to mae it sleep
    - args:
    - sleep
    - infinity

6. Add the args to the Pod file

apiVersion: v1
kind: Pod
metadata:
  name: lab7pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: lab7pvc
  containers:
    - name: task-pv-container
      image: busybox
      args:
        - sleep
        - infinity
      volumeMounts:
        - mountPath: "/data"
          name: task-pv-storage

7. Now copy storage file from kubectl cp

kubectl cp --help
kubectl cp /etc/hosts lab7pod:/data

Log into containers.
kubectl exec -it lab7pod -n default -- sh
/ # ls
bin    data   dev    etc    home   lib    lib64  proc   root   sys    tmp    usr    var
/ # cd data/
/data # ls
hosts
/data # cat hosts

kubectl describe pv lab7-pv : will show the Path of the local mount point.


# Lession 8 : Deployments
Scalable Applications
    - Standalone Pod cannot be scaled
    - Deployment is standard resource for running scable statelss applications
    - DaemonSet is a modification to a Deployment that runs one application instance on ever
    cluster node, unless a node taint prevents it from doing so.


* Deployments use ReplicaSet to scale application
* For automatic application scaling, use HorizontalPodAutoscaler
* Deployments offer updateStrategy, a property that allows applications to be upgraded
without experiencing any downtime.

## Labels and Selectors
* A label is a key-value pair that is commonly used in Kubernetes
    - Deployment created with kubectl create deploy automatically get the label "app=deploymentname"
    - Pods stated with kubectl run automatically get the lable run=podname
* The purpose of the label is to connect different objects
    - Deployments tracking Pords
    - Services connecting to Pods

* The selector is used on resources to specify which label to track
* As a command line argument, --selector can be used to fliter output on the presence of a label

To filter the tag use :
kubectl get all --selector app=<app_name>
kubectl get all --selector app=myapp

To look at the yaml of an deployment
command: kubectl get deploy <app_name> -o yaml | less

kubectl create deploy webapp --image=nginx --replicas=3
deployment.apps/webapp created
linux2@kubernetes:~/storage_task$ kubectl get deploy webapp -o yaml | less
linux2@kubernetes:~/storage_task$ kubectl get all --selector app=webapp
NAME                         READY   STATUS    RESTARTS   AGE
pod/webapp-c89577dd5-7qvmn   1/1     Running   0          30s
pod/webapp-c89577dd5-kv29p   1/1     Running   0          30s
pod/webapp-c89577dd5-wt7lj   1/1     Running   0          30s

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp   3/3     3            3           30s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-c89577dd5   3         3         3       30s
linux2@kubernetes:~/storage_task$ 

Show all the labels : kubectl get pods -A --show-labels


## Managing Labels 

ubectl get deploy -A
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
default       firstapp                  1/1     1            1           5d22h
default       webapp                    3/3     3            3           3m45s
kube-system   calico-kube-controllers   1/1     1            1           5d23h
kube-system   coredns                   2/2     2            2           5d23h
linux2@kubernetes:~/storage_task$ kubectl get deploy -A --show-labels
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
default       firstapp                  1/1     1            1           5d22h   app=firstapp
default       webapp                    3/3     3            3           3m59s   app=webapp
kube-system   calico-kube-controllers   1/1     1            1           5d23h   k8s-app=calico-kube-controllers
kube-system   coredns                   2/2     2            2           5d23h   k8s-app=kube-dns


- Use kubectl label to manually set labels
- Notice that when a label is set manually to a Deployment after its creation it will not be inherited to child resources.
- kubectl label key - to remove the label key with its value


kubectl create deploy bluelabel --image=nginx
deployment.apps/bluelabel created


linux2@kubernetes:~/storage_task$ kubectl get deploy -A --show-labels
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
default       bluelabel                 1/1     1            1           22s     app=bluelabel
default       firstapp                  1/1     1            1           5d22h   app=firstapp
default       webapp                    3/3     3            3           7m18s   app=webapp
kube-system   calico-kube-controllers   1/1     1            1           5d23h   k8s-app=calico-kube-controllers
kube-system   coredns                   2/2     2            2           5d23h   k8s-app=kube-dns

Add additonal label.
linux2@kubernetes:~/storage_task$ kubectl label deploy bluelabel state=demo
deployment.apps/bluelabel labeled


linux2@kubernetes:~/storage_task$ kubectl get deploy -A --show-labels
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
default       bluelabel                 1/1     1            1           101s    app=bluelabel,state=demo
default       firstapp                  1/1     1            1           5d22h   app=firstapp
default       webapp                    3/3     3            3           8m37s   app=webapp
kube-system   calico-kube-controllers   1/1     1            1           5d23h   k8s-app=calico-kube-controllers
kube-system   coredns                   2/2     2            2           5d23h   k8s-app=kube-dns
linux2@kubernetes:~/storage_task$ 


kubectl get all --selector app=<label> : will show label of podsa dn replicas
kubectl get deploy --selector state=<app_name>: Will only show deployments

To describe an Deployment app

kubectl describe deployments.apps bluelabel

kubectl describe deployments.apps <deploy_name>

To unlabel a Deployment 
linux2@kubernetes:~/storage_task$ kubectl get all --selector app=bluelabel
NAME                             READY   STATUS    RESTARTS   AGE
pod/bluelabel-6655494bb4-6glcs   1/1     Running   0          11m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/bluelabel   1/1     1            1           11m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/bluelabel-6655494bb4   1         1         1       11m

rror from server (NotFound): pods "bluelabel" not found

Remove label from Pod
linux2@kubernetes:~/storage_task$ kubectl label pod bluelabel-6655494bb4-6glcs app-
pod/bluelabel-6655494bb4-6glcs unlabeled


## Understanding Annotation
- Annotations are used to store additional information
    - This can be generic information, like notes
    - Annotations are also used by certain Kubernetes commands to store auto managed information in the resource.

Creates Notes

kubectl create deploy notes --image=nginx --dry-run=client -o yaml > notes.yaml
kubectl apply -f notes.yaml
kubectl get deploy notes -o yaml | less
kubectl annotate deploy notes newnote="my note"

## Deployment Scalability
* Deployments use ReplicaSet to manage the expected number of application instances
* When an application is stated using kubectl create deploy .. -replicas=3, the ReplicaSet ensures the desired number of instances is running.
* To manually manage application scalability, use kubectl scale deploymentname --replicas=3

Create and scale deployment replica set

kubectl create deploy scaledapp --image=nginx --replicas=3

kubectl get all --selector app=scaledapp

Delete Replica Set
-----------------
kubectl delete pod <replica_set_id>

kubectl get all --selector app=scaledapp

Scale down replica set
-----------------------
kubectl scale deployment scaleapp --replicas=2

kubectl get all --selector app=scaledapp


## Deployment Updates
To manage how applications are updated, update strategy
    - strategy.type.rollingUpdate : updates app instances in batches to ensure app functionality continues to be offered at any time
        - RollingUpdate ensure different version of app will be running
    - App that dont support offering multiple version simultanesously set strategy.type.recreate
        - Recreate strategy brings down all app instances after which the new app version is brought up.

* Managing Rolling Updates
    - Manage Rolling Update two parameters are used:
        maxSurge specifies how many app instances can be running during the update above the regular number of app instances
    - maxUnavailable defines how many app instances can be temp unavailable
        - Both parameter take an absolute number or a percentage as arggument.

### Demo ###
Create Deployment and Find Strategy and Perform a Rolling update


linux2@kubernetes:~$ kubectl create deploy upapp --image=nginx:1.17 --replicas=5
deployment.apps/upapp created
linux2@kubernetes:~$ kubectl get deployments.app upapp -o yaml | grep -A5 strategy
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:

We set Rolling Updates to 25%, Update image with set image

kubectl get all --selector app=upapp
NAME                         READY   STATUS              RESTARTS   AGE
pod/upapp-696c9dfbd-74l7d    0/1     ContainerCreating   0          2s
pod/upapp-696c9dfbd-dmwxw    0/1     ContainerCreating   0          3s
pod/upapp-696c9dfbd-hwzxg    1/1     Running             0          11s
pod/upapp-696c9dfbd-sb6t6    1/1     Running             0          11s
pod/upapp-696c9dfbd-vrwhv    1/1     Running             0          11s
pod/upapp-7b94687d67-cp2l9   1/1     Terminating         0          3m22s
pod/upapp-7b94687d67-j76x5   1/1     Terminating         0          3m22s
pod/upapp-7b94687d67-k486m   1/1     Terminating         0          3m22s
pod/upapp-7b94687d67-qpfzp   1/1     Running             0          3m22s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/upapp   4/5     5            4           3m22s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/upapp-696c9dfbd    5         5         3       11s
replicaset.apps/upapp-7b94687d67   1         1         1       3m22s

kubectl edit deployment.apps upapp


Now we are going to change the deployment strategy to recreate

Remove three lines until type

 strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate

Change type to Recrete

Now change the deployment to a higer nginx version

kubectl set image deploy/upapp nginx=nginx:1.19
deployment.apps/upapp image updated
linux2@kubernetes:~$ kubectl get all --selector app=upapp
NAME                         READY   STATUS              RESTARTS   AGE
pod/upapp-774dddc5cc-56jd7   0/1     ContainerCreating   0          1s
pod/upapp-774dddc5cc-dr4m9   0/1     ContainerCreating   0          1s
pod/upapp-774dddc5cc-gt86l   0/1     ContainerCreating   0          1s
pod/upapp-774dddc5cc-kcwmr   0/1     ContainerCreating   0          1s
pod/upapp-774dddc5cc-n2dzc   0/1     ContainerCreating   0          1s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/upapp   0/5     5            0           13m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/upapp-696c9dfbd    0         0         0       10m
replicaset.apps/upapp-774dddc5cc   5         5         0       1s
replicaset.apps/upapp-7b94687d67   0         0         0       13m
linux2@kubernetes:~$ 


## Deployment History
* Understanding Deployment History
    - During the update procedure, the Deployment creates a new ReplicaSet that uses the new properties
    - The old ReplicaSet is kept, but the number of Pods will be set to 0
    - Command to Rollout:
        kubectl rollout history : will show the rollout history of a specific deployment, which can easily be reverted as well
         kubectl rollout history deployment mynginx --revision=1 to observe changes between versions


-rw-rw-r--  1 linux2 linux2   382 Dec 17 13:16 rolling.yaml
linux2@kubernetes:~$ kubectl create -f rolling.yaml
deployment.apps/rolling-nginx created
linux2@kubernetes:~$ kubectl rollout history deployment
deployment.apps/bluelabel 
REVISION  CHANGE-CAUSE
1         <none>

deployment.apps/firstapp 
REVISION  CHANGE-CAUSE
1         <none>

deployment.apps/rolling-nginx 
REVISION  CHANGE-CAUSE
1         <none>

deployment.apps/upapp 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>

deployment.apps/webapp 
REVISION  CHANGE-CAUSE
1         <none>


------
Edit the container verison to  nginx: 1.17

kubectl edit deployment.apps rolling-nginx
deployment.apps/rolling-nginx edited
linux2@kubernetes:~$ kubectl rollout history deployment rolling-nginx
deployment.apps/rolling-nginx 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

linux2@kubernetes:~$ kubectl describe deployments rolling-nginx
Name:                   rolling-nginx
Namespace:              default
CreationTimestamp:      Wed, 17 Dec 2025 13:16:21 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 2 max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:         nginx:1.17
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  rolling-nginx-6dcbb79877 (0/0 replicas created)
NewReplicaSet:   rolling-nginx-79cdc8bb4c (4/4 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m18s  deployment-controller  Scaled up replica set rolling-nginx-6dcbb79877 from 0 to 4
  Normal  ScalingReplicaSet  37s    deployment-controller  Scaled up replica set rolling-nginx-79cdc8bb4c from 0 to 2
  Normal  ScalingReplicaSet  37s    deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 4 to 3
  Normal  ScalingReplicaSet  37s    deployment-controller  Scaled up replica set rolling-nginx-79cdc8bb4c from 2 to 3
  Normal  ScalingReplicaSet  32s    deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 3 to 2
  Normal  ScalingReplicaSet  32s    deployment-controller  Scaled up replica set rolling-nginx-79cdc8bb4c from 3 to 4
  Normal  ScalingReplicaSet  32s    deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 2 to 1
  Normal  ScalingReplicaSet  32s    deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 1 to 0

------
Check rollout history and rollback
linux2@kubernetes:~$ kubectl rollout history deployment rolling-nginx --revision=2
deployment.apps/rolling-nginx with revision #2
Pod Template:
  Labels:	app=nginx
	pod-template-hash=79cdc8bb4c
  Containers:
   nginx:
    Image:	nginx:1.17
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
  Node-Selectors:	<none>
  Tolerations:	<none>

linux2@kubernetes:~$ kubectl rollout history deployment rolling-nginx --revision=1
deployment.apps/rolling-nginx with revision #1
Pod Template:
  Labels:	app=nginx
	pod-template-hash=6dcbb79877
  Containers:
   nginx:
    Image:	nginx:1.8
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
  Node-Selectors:	<none>
  Tolerations:	<none>

linux2@kubernetes:~$ # Rollback app
linux2@kubernetes:~$ kubectl rollout undo deployment rolling-nginx --to-revision=1
deployment.apps/rolling-nginx rolled back
linux2@kubernetes:~$ kubectl describe deployments rolling-nginx
Name:                   rolling-nginx
Namespace:              default
CreationTimestamp:      Wed, 17 Dec 2025 13:16:21 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 3
Selector:               app=nginx
Replicas:               4 desired | 3 updated | 6 total | 3 available | 3 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 2 max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:         nginx:1.8
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    ReplicaSetUpdated
OldReplicaSets:  rolling-nginx-79cdc8bb4c (3/3 replicas created)
NewReplicaSet:   rolling-nginx-6dcbb79877 (3/3 replicas created)
Events:
  Type    Reason             Age                From                   Message
  ----    ------             ----               ----                   -------
  Normal  ScalingReplicaSet  5m8s               deployment-controller  Scaled up replica set rolling-nginx-6dcbb79877 from 0 to 4
  Normal  ScalingReplicaSet  3m27s              deployment-controller  Scaled up replica set rolling-nginx-79cdc8bb4c from 0 to 2
  Normal  ScalingReplicaSet  3m27s              deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 4 to 3
  Normal  ScalingReplicaSet  3m27s              deployment-controller  Scaled up replica set rolling-nginx-79cdc8bb4c from 2 to 3
  Normal  ScalingReplicaSet  3m22s              deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 3 to 2
  Normal  ScalingReplicaSet  3m22s              deployment-controller  Scaled up replica set rolling-nginx-79cdc8bb4c from 3 to 4
  Normal  ScalingReplicaSet  3m22s              deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 2 to 1
  Normal  ScalingReplicaSet  3m22s              deployment-controller  Scaled down replica set rolling-nginx-6dcbb79877 from 1 to 0
  Normal  ScalingReplicaSet  12s                deployment-controller  Scaled up replica set rolling-nginx-6dcbb79877 from 0 to 2
  Normal  ScalingReplicaSet  12s (x2 over 12s)  deployment-controller  (combined from similar events): Scaled up replica set rolling-nginx-6dcbb79877 from 2 to 3


## StatefulSet
* StatefulSet maintains the identity of Pords, even if they are restarted
    - Required by stateful app, like databases
* Stateful Set is required when following:
    - Stable, unique network identifiers
    - Stable, persistent storage
    - Ordered, graceful deployment and scaling
    - Ordered and automated rolling updates
* Setting up a Stateful is easier when using helm charts

* Storage must be provisioned by a PersistentVolume and will be claimed by the StatefulSet volumeClaimTemplate, which generates a PVC
    - Deleting a StatefulSet does not not delete associated volumes
    - While creating the Pords, every next Pod copies the config of the Pod before and adds its unique identiy.
    - As a result, Pods are created one by one and it will take a while before all Pod instances are available
    - As a result, Pods are created one-by-one, and it will take a while before Pods are available.

* Stateful Storage
- The StatefulSet volumeClaimTemplate generates a PVC that connects to a PV
    - To connect to an existing PV, or request storage from a specific StorageClass, the storageClassName property is used.
    - If no storageClassName property is set, storage is obtained from the default StorageClass
    - If no StorageClass is available, make sure that PV's with the appropriate storageClass property are available.

## Daemon Set
* A DaemonSet is a Deployment that starts one Pod instance on every node in the cluster
    - Useful when agent needs to be available
    - When nodes are added or removed, the DaemonSet automatically changes the number of Pods accordingly.
Daemon Set does not need a replica and strategy 

kubectl create deploy daemonnginx --image=nginx --dry-run=client -o yaml > daemon.yaml

List daemon and pods 
kubectl get ds,pods

## Autoscaling 
- In a real clusters, Pods are automatically scaled based on resource usage properties that are collected by the metrics server
- The HorizontalPodAutoscaler observes usage statistics, and after passiing a threshold will add additional replicas

Demo
-------
1. Build Docker file from ckad file:

docker build -t php-apache .

2. inux2@kubernetes:~/ckad/autoscaling$ docker images
                                                            i Info →   U  In Use
IMAGE                           ID             DISK USAGE   CONTENT SIZE   EXTRA
gcr.io/k8s-minikube/kicbase:v0.0.48
                                41454ef774d0       1.84GB          512MB        
gcr.io/k8s-minikube/kicbase@sha256:7171c97a51623558720f8e5878e4f4637da093e2f2ed589997bedc6c1549b2b1
                                7171c97a5162       1.84GB          512MB    U   
mariadb:latest                  e1bcd6f85781        467MB          111MB        
php-apache:latest               98e39e2f7433        513MB          130MB        
linux2@kubernetes:~/ckad/autoscaling$ 


3. Now apply the hpa.yml file

kubectl apply -f hpa.yaml
deployment.apps/php-apache created
service/php-apache created



linux2@kubernetes:~/ckad/autoscaling$ kubectl get deploy,service
NAME                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/bluelabel       1/1     1            1           24h
deployment.apps/firstapp        1/1     1            1           6d23h
deployment.apps/php-apache      1/1     1            1           71s
deployment.apps/rolling-nginx   4/4     4            4           6h36m
deployment.apps/upapp           5/5     5            5           7h
deployment.apps/webapp          3/3     3            3           24h

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   6d23h
service/php-apache   ClusterIP   10.96.146.39   <none>        80/TCP    71s
linux2@kubernetes:~/ckad/autoscaling$ 

4. Autoscale the deployment

kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
horizontalpodautoscaler.autoscaling/php-apache autoscaled
linux2@kubernetes:~/ckad/autoscaling$ kubectl get hpa
NAME         REFERENCE               TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   cpu: <unknown>/50%   1         10        1          16s
linux2@kubernetes:~/ckad/autoscaling$ 


5. Now time to Create some stats - Open new terminal using load-generator

ubectl get hpa
NAME         REFERENCE               TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   cpu: <unknown>/50%   1         10        1          16s
linux2@kubernetes:~/ckad/autoscaling$ kubectl run -it load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done
> "
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!

This will generator ok message for logs

6. Now go back to first tab and look at the increase of metrics

Create a metric server in kubernetes

command: 

minikube addons enable metrics-server
💡  metrics-server is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
🌟  The 'metrics-server' addon is enabled



Lession 8 Lab: Managin Deployments
- Create a YAML file that starts a deployment for nginx
    - It should use image version 1.9 and start 5 replicas
    - The deployment should have the label type=proxy
- Also configure the deployment such that when it upgraded, no more than 2 Pods will be unavailable at the same time

1. Create yaml file 

kubectl create deploy lab8deploy --image=nginx:1.9 --replicas=5 --dry-run=client -o yaml > lab8.yaml

2. To generate a sample file run, this will give you the parameter of strategy 
kubectl create deploy dummy --image=nginx  -o yam

3. Take the strategy from the above yaml format and put it into your new yaml file.

strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:

4. Kubectl apply -f lab9_lab.yaml 
5. List pods : kubectl get all

## Installing Kubernetes Applications
- The Helm Package Manager
* Hwlm is Kubernetes package manager and used to streamline installing and managing Kubernetes application
    - Helm consists of helm tool, which needs to be installed, and a chart
    - Helm package contains: 1. Description of the package 2. One or more templates containing Kubernetes manifest files
    - Charts can be stored locally, or accessed from remote Helm repos

* Install the helm Binary
    - Fetch binary from github. Get the latest: https://github.com/helm/helm/releases/tag/v4.0.4

    - Untar the file and move it to /usr/local/bin

    tar -xvf helm-v4.0.4-linux-amd64.tar.gz 
linux-amd64/
linux-amd64/LICENSE
linux-amd64/README.md
linux-amd64/helm

    linux2@kubernetes:~/Downloads$ sudo !!
sudo mv linux-amd64/helm /usr/local/bin/
linux2@kubernetes:~/Downloads$ helm version
version.BuildInfo{Version:"v4.0.4", GitCommit:"8650e1dad9e6ae38b41f60b712af9218a0d8cc11", GitTreeState:"clean", GoVersion:"go1.25.5", KubeClientVersion:"v1.34"}
linux2@kubernetes:~/Downloads$ 


    - Verify helm version


helm version
version.BuildInfo{Version:"v4.0.4", GitCommit:"8650e1dad9e6ae38b41f60b712af9218a0d8cc11", GitTreeState:"clean", GoVersion:"go1.25.5", KubeClientVersion:"v1.34"}

----

To enable bash completetion for helm
command: source <(helm completion bash)

* Helm Chart doesn't come with a default repo
* The main site for finding Helm charts is through https://artifacthub.io
* Search for specific software run and instlal it to run the Kubernetes Dashboard
    - helm repo add kubernetes-dashboard
    - https://kubernetes.github.io/dashboard
    - helm install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard (Create the local app name "kubernetes-dashboard by installing applicaton "kubernetes-dashboard from repo.)

Install bitnami repo 

helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
linux2@kubernetes:~$ helm repo list
NAME   	URL                               
bitnami	https://charts.bitnami.com/bitnami
linux2@kubernetes:~$ 


Search repo for a particular package : 

helm search repo <package>
helm search repo file

* Installing Helm Charts
    - After adding repo, use helm repo update to ensure access to the most up to date infor
    - helm install <name> <chart> to install the chart with default parameters
        - Chart could be installed multiple times.
    - After installation, use helm list to list currently installed charts
        - To delete : helm delete chart/

     helm install bitnami/mysql --generate-name

     NAME: mysql-1766262289
LAST DEPLOYED: Sat Dec 20 20:24:53 2025
NAMESPACE: default
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
TEST SUITE: None
NOTES:
CHART NAME: mysql
CHART VERSION: 14.0.3
APP VERSION: 9.4.0

kubectl get all
NAME                           READY   STATUS    RESTARTS        AGE
pod/dummy-7df9b6c9bb-ljglf     1/1     Running   1 (9m26s ago)   22h
pod/mysql-1766262289-0         0/1     Pending   0               52s
pod/webapp2-77bdbbd768-2fs4k   1/1     Running   1 (9m26s ago)   22h
pod/webapp2-77bdbbd768-ct9dx   1/1     Running   1 (9m26s ago)   22h
pod/webapp2-77bdbbd768-h8dvq   1/1     Running   1 (9m26s ago)   22h
pod/webapp2-77bdbbd768-tdnk4   1/1     Running   1 (9m26s ago)   22h
pod/webapp2-77bdbbd768-xszqp   1/1     Running   1 (9m26s ago)   22h

NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/kubernetes                  ClusterIP   10.96.0.1        <none>        443/TCP    10d
service/mysql-1766262289            ClusterIP   10.106.123.155   <none>        3306/TCP   52s
service/mysql-1766262289-headless   ClusterIP   None             <none>        3306/TCP   52s
service/php-apache                  ClusterIP   10.96.146.39     <none>        80/TCP     3d

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dummy     1/1     1            1           22h
deployment.apps/webapp2   5/5     5            5           2d23h

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/dummy-7df9b6c9bb     1         1         1       22h
replicaset.apps/webapp2-77bdbbd768   5         5         5       2d23h
replicaset.apps/webapp2-7b784b5687   0         0         0       2d23h
replicaset.apps/webapp2-859b6887bf   0         0         0       2d23h
replicaset.apps/webapp2-c86f9dc6b    0         0         0       2d23h

NAME                                READY   AGE
statefulset.apps/mysql-1766262289   0/1     52s

NAME                                             REFERENCE               TARGETS              MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/php-apache   Deployment/php-apache   cpu: <unknown>/50%   1         10        1          3d
linux2@kubernetes:~$ 

This will show the pod mysql and the statefulset that is running

A StatefulSet is a Kubernetes workload type used to run stateful applications—apps that need to keep stable identity, storage, or ordering.

Think of it as:

A Deployment, but for apps that care about who they are and where their data lives.

Deployments are used for website, api, background workers
Stateful sets used for databases, messag brokers, clustered systems

helm show chart bitnami/mysql : show information about the chart

helm show all bitnami/mysql

command: helm show chart <repo>/chart_name

## Managing Applications with Helm
* Customizing Helm Applications
    - A Helm chart consist of templates to which specific values are applied
    - The values are stored in the values.yaml file, within the hel chart
    - Use helm show values to list current values
    - Create a custom values.yaml which will be merged with the generic values.yaml file: helm install .. -values values.yaml
    - When upgrading an application, you have to use --values values.yaml as well


* Overriding Values 
    - Default values.yaml file which is part of the helm chart contains
    default values which are defined as key-value pair
    - Check doc for information about values: artifacthub.io
    - While installing chart use custom values.yaml if
    - helm install ... -set key=value to set indivdiual values

When installing helm chart, you can set certain values, so that when downloading the packages it can ovveride the properties set.

helm show values bitnami/nginx | grep commonLabels
## @param commonLabels Add labels to all the deployed resources
commonLabels: {}
linux2@kubernetes:~$ vi values.yml


Inside of values.yaml

commonLabels: "type: helmapp"
replicaCount: 3

helm install bitnami/nginx --generate-name --values values.yml

helm list
NAME            	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART       	APP VERSION
mysql-1766262289	default  	1       	2025-12-20 20:24:53.790689454 +0000 UTC	deployed	mysql-14.0.3	9.4.0      
nginx-1766318189	default  	1       	2025-12-21 11:56:32.597517471 +0000 UTC	deployed	nginx-22.3.9	1.29.4     


# Verify the values
linux2@kubernetes:~$ helm get values nginx-1766318189
USER-SUPPLIED VALUES:
commonLabels: 'type: helmapp'
replicaCount: 3
linux2@kubernetes:~$ 


Shows all the values withh --all

 helm get values --all nginx-1766318189

Verify deployment :

kubectl get deploy --show-labels
NAME               READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
dummy              1/1     1            1           38h     app=dummy
nginx-1766318189   3/3     3            3           5m13s   app.kubernetes.io/instance=nginx-1766318189,app.kubernetes.io/managed-by=Helm,app.kubernetes.io/name=nginx,app.kubernetes.io/version=1.29.4,helm.sh/chart=nginx-22.3.9,type=helmapp
webapp2            5/5     5            5           3d15h   app=webapp2,type=proxy
linux2@kubernetes:~$ 


* Helm Upgrade
- Helm chart is the package you install from the Helm repo
- Helm installation is an installed instance of that package
- A Helm release is a combination between a chart and an installation
    - Ex. To run a nginx installation withoue exposing it
        command: helm install bitginx bitnami/nginx --set ingress.enabled=false

        To make app accessbile, use helm update bitginx bitnami/nginx --set ingress.enabled=true
Verify you have the most up to date Helm repo
    - Fetch the latest version of charts from the repo
        helm repo update
    - Use helm upgrade bitginx bitnami/nginx to upgrade to the latest version of the application

Alternative way to use kubectl is kustomize 
    - Kustomize is a Kubernetes feature that uses a file with the name kustomization.yaml to apply changes to a set of resources
        - Use kubectl apply -k ./ in the directory with kustomization.yaml and the files it refers to apply changes
        kubectl delete -k ./ delete resources created by kustomization.


Lab9 : Install the Hashicorp Lab

Open a browser and go to artifacthub.io
type vault

This will provide the steps to install the repo and chart



linux2@kubernetes:~$ helm repo add hashicorp https://helm.releases.hashicorp.com
"hashicorp" has been added to your repositories
linux2@kubernetes:~$ helm install my-hash-vault hashicorp/vault --version 0.31.0
NAME: my-hash-vault
LAST DEPLOYED: Sun Dec 21 12:23:27 2025
NAMESPACE: default
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
NOTES:
Thank you for installing HashiCorp Vault!

Now that you have deployed Vault, you should look over the docs on using
Vault with Kubernetes available here:

https://developer.hashicorp.com/vault/docs


Your release is named my-hash-vault. To learn more about the release, try:

  $ helm status my-hash-vault
  $ helm get manifest my-hash-vault
linux2@kubernetes:~$ 

# Services and Networking
- A Service is an API resource that is used to exposte a set of Pods
    - Services are applying round-robin load balancing to forward traffic to specific Pods
    - The set of Pods that is targeted by a Service is determined by a selector and include these in the Service.
    If the Pods are added ore removed, they immediately show up in the service

* Services and Decoupling
    - Servies exist independenttly from the app the provide access to
    - Service needs to be created independenlty of the app and after removing an app, it needs to be removed separetly
    - THe only thing they do is watch for Pods that have a specific label set matching the selector that is specified in the service
    - That means the one service can provide access to pods in multiple deployments and kubernetes will automatically load balance between thse Pods (used in canary deployments)


* Service Types
 - ClusterIP: this default type exposes the service on an internal cluster IP address
 - NodePort: allocates a specific port on the node that forwards to the service IP address on the cluster network
 - LoadBalancer : provisions an external load balancer to hadnle incoming traffic to apps in public cloud
 - ExternalName : work on DNS names, redirection is happening at a DNS level, which is ful in migration
 - Headless: a service used in cases where direct communication with Pods is required which is ued in StatefullSet.

 * Creating Services
    - kubectl expose can be used to create  Services, providing access to DEployments, ReplicaSets, Pods or other servies
    - kubectl expose exposes a Deployment, which allocates its Pods as the servie endpoint
    - kubectl create service can be used as an alternative solution to create services
    - Whil creating a service , the --port arguement must be specificed to indicate the port on whicch the service will be listening for incoming traffic

Service Port
targetPort: the port on the app(container) that the servie addresses
port: the port on which the service is accessible
nodePort: that port that is exposed externally while using the NoePort service type.


inux2@kubernetes:~$ kubectl create deployment nginxsvc --image=nginx
deployment.apps/nginxsvc created
linux2@kubernetes:~$ kubectl scale deployment nginxsvc --replicas=3
deployment.apps/nginxsvc scaled
linux2@kubernetes:~$ kubectl get deploy
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
dummy                          1/1     1            1           39h
my-hash-vault-agent-injector   1/1     1            1           32m
nginx-1766318189               3/3     3            3           59m
nginxsvc                       3/3     3            3           35s
webapp2                        5/5     5            5           3d15h
linux2@kubernetes:~$ kubectl expose deployment nginxsvc --port=80
service/nginxsvc exposed
linux2@kubernetes:~$ kubectl get all --selector app=nginxsvc
NAME                            READY   STATUS    RESTARTS   AGE
pod/nginxsvc-5985d79656-n45rg   1/1     Running   0          73s
pod/nginxsvc-5985d79656-xlght   1/1     Running   0          89s
pod/nginxsvc-5985d79656-xz5hz   1/1     Running   0          73s

NAME               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/nginxsvc   ClusterIP   10.96.38.58   <none>        80/TCP    24s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginxsvc   3/3     3            3           89s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/nginxsvc-5985d79656   3         3         3       89s
linux2@kubernetes:~$ 


linux2@kubernetes:~$ kubectl describe svc nginxsvc
Name:                     nginxsvc
Namespace:                default
Labels:                   app=nginxsvc
Annotations:              <none>
Selector:                 app=nginxsvc
Type:                     ClusterIP
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.96.38.58
IPs:                      10.96.38.58
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
Endpoints:                172.16.192.94:80,172.16.192.96:80,172.16.192.69:80
Session Affinity:         None
Internal Traffic Policy:  Cluster
Events:                   <none>
linux2@kubernetes:~$ 


Verify the output in yaml format:

kubectl get svc nginxsvc -o yaml

Get endpoints :
kubectl get endpoints
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME                               ENDPOINTS                                                                 AGE
kubernetes                         10.0.2.15:6443                                                            10d
my-hash-vault                      <none>                                                                    38m
my-hash-vault-agent-injector-svc   172.16.192.90:8080                                                        38m
my-hash-vault-internal             <none>                                                                    38m
mysql-1766262289                   <none>                                                                    16h
mysql-1766262289-headless          <none>                                                                    16h
nginx-1766318189                   172.16.192.109:8443,172.16.192.121:8443,172.16.192.124:8443 + 3 more...   65m
nginxsvc                           172.16.192.69:80,172.16.192.94:80,172.16.192.96:80                        5m19s
php-apache                         <none>                                               

Get the Svc's available

inux2@kubernetes:~$ kubectl get endpoints
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME                               ENDPOINTS                                                                 AGE
kubernetes                         10.0.2.15:6443                                                            10d
my-hash-vault                      <none>                                                                    38m
my-hash-vault-agent-injector-svc   172.16.192.90:8080                                                        38m
my-hash-vault-internal             <none>                                                                    38m
mysql-1766262289                   <none>                                                                    16h
mysql-1766262289-headless          <none>                                                                    16h
nginx-1766318189                   172.16.192.109:8443,172.16.192.121:8443,172.16.192.124:8443 + 3 more...   65m
nginxsvc                           172.16.192.69:80,172.16.192.94:80,172.16.192.96:80                        5m19s
php-apache                         <none>                                                                    3d17h
linux2@kubernetes:~$ kubectl get svc
NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
kubernetes                         ClusterIP      10.96.0.1        <none>        443/TCP                      10d
my-hash-vault                      ClusterIP      10.103.33.235    <none>        8200/TCP,8201/TCP            39m
my-hash-vault-agent-injector-svc   ClusterIP      10.108.96.78     <none>        443/TCP                      39m
my-hash-vault-internal             ClusterIP      None             <none>        8200/TCP,8201/TCP            39m
mysql-1766262289                   ClusterIP      10.106.123.155   <none>        3306/TCP                     16h
mysql-1766262289-headless          ClusterIP      None             <none>        3306/TCP                     16h
nginx-1766318189                   LoadBalancer   10.98.98.30      <pending>     80:30397/TCP,443:32407/TCP   66m
nginxsvc                           ClusterIP      10.96.38.58      <none>        80/TCP                       6m24s
php-apache                         ClusterIP      10.96.146.39     <none>        80/TCP                       3d17h
linux2@kubernetes:~$ 


Get your svc address and log into minikube.
Curl your service ip. From minikube this will work, because you are curling internally.

To fix edit the service file for nginxsvc
Change ClusterIP to NodePort

Then you will be able to reach webpage by curling minikube ip with none
standard port you will get from kubectl get svc ip.

## Service Resources in Microservices 
- Understanding Microservices
 - Microservices architecture, different frontend and backend Pods used to provide the application
    - Backend Pods (like databases) should be exposed internally only, using the ClusterIP Service type
    - Frontend Pods (like webservers) should be exposed for external access, using the NodePort Service type of the Ingress resource
    - For more advanced traffic management in microservices, a service  mesh can be used.

## Service and DNS
 - Exposed Services automatically register with the Kubernetes intenal coredns DNS server 
    - The standard DNS name is composed as servicename.namespace.svc.clustername
    - As a result, Pods within the same namespace can access servicename by using its short name
    - To access servicenames in other Namespaces, the fully qualified domain must be used.

Demo
------
Describing the kube-dns from the kube-system namespace

linux2@kubernetes:~$ kubectl get ns
NAME              STATUS   AGE
default           Active   11d
kube-node-lease   Active   11d
kube-public       Active   11d
kube-system       Active   11d

linux2@kubernetes:~$ kubectl get svc -n kube-system
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
kube-dns         ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP,9153/TCP   11d
metrics-server   ClusterIP   10.105.222.82   <none>        443/TCP                  6h42m
linux2@kubernetes:~$ 

kubectl describe svc -n kube-system kube-dns
Name:                     kube-dns
Namespace:                kube-system
Labels:                   k8s-app=kube-dns
                          kubernetes.io/cluster-service=true
                          kubernetes.io/name=CoreDNS

The Endpoint Ip is the IP that provides internal DNS
1. Create a new namespace named elsewhere
2. Create a pod named nginxpod in  elsewhere namespace nginx image
3. Expose port 80 on new pod

linux2@kubernetes:~$ kubectl create ns elsewhere
namespace/elsewhere created
linux2@kubernetes:~$ kubectl run nginxpod -n elsewhere --image=nginx
pod/nginxpod created
linux2@kubernetes:~$ kubectl expose -n elsewhere pod nginxpod --port=80

service/nginxpod exposed


---------------
4. Create a testpod in regular namespace
    kubectl run testpod --image=busybox -- sleep infinity

5. Create new pod in default namespace
kubectl run testpod --image=busybox -- sleep infinity
pod/testpod created

6. kubectl exec -it testpod -- cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local mynetworksettings.com
options ndots:5
linux2@kubernetes:~$ 

7. Since we exposed port 80 on the elsehwere pod and we know fqdn of the dns name we can use that.

8. Lets query the pod running in the elsewhere namespace

kubectl exec -it testpod -- wget --spider --timeout=1 nginxpod.elsewhere.svc.cluster.local
Connecting to nginxpod.elsewhere.svc.cluster.local (10.110.159.82:80)
remote file exists


## Network Policy

By default, there are no restrictions to network traffic in k8s
    - Pod can always communicate, evne if they're in other namespaces
    - To limit this, networkPolicies can be used
    - NetworkPolicies need to be supported by the network plugin though
    - If in a policy there is no match , traffic will be denied.
    - If no NetworkPolicy is used , all traffic is allowed.
* Network Policy three different identifiers
    - podSelector: specifies a label to match Pods
    - namespaceSelector: used to grant access to specific namespace
    - ipBlock: marks a range of IP addresses that is allowd. Notice that traffic to and from the node where a Pod is running is always allowed
        - When defining a Pod or namespace networkPolicy a selector label is used to specify what traffic is allowed to and from the Pods that match the selector.  
            - NetworkPolicies do not conflict

## Demo
Pre
Verify that the calico-node Pod is running in kube-system namespace.

wget --spider is a safe “check only” mode.
It does not download anything — it just tests whether a URL is reachable.

linux2@kubernetes:~/ckad$ kubectl exec -it busybox -- wget --spider --timeout=1 nginx
Connecting to nginx (10.104.68.158:80)
remote file exists
linux2@kubernetes:~/ckad$ kubectl describe networkpolicy
Name:         access-nginx
Namespace:    default
Created on:   2025-12-22 21:35:05 +0000 UTC
Labels:       <none>
Annotations:  <none>
Spec:
  PodSelector:     app=nginx
  Allowing ingress traffic:
    To Port: <any> (traffic allowed to all ports)
    From:
      PodSelector: access=true
  Not affecting egress traffic
  Policy Types: Ingress

Add label to the busybox pod, so that it can be added to network policy.

linux2@kubernetes:~/ckad$ kubectl label pod busybox access=true
pod/busybox labeled

New Pod has the new label :

kubectl describe pod busybox
Name:             busybox
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Mon, 22 Dec 2025 21:35:05 +0000
Labels:           access=true
                  app=sleepy

## Advanced Networking : Gateway API and Istio
- Gateway API provides routing and traffic management policies
    - uses custom resources for managing incoming ingress and outogin egress traffic
- Istio : a service mesh and makes managing complex relations between applications in a microservice easier
    - Provides rules for managing traffic in microservices. Optional in additon to network policies.

Lesson 10: Managing Services
- Create a namespace with the name "Remote"
- In the remote Namespace, run an nginx pod with the name "Remoteweb" and expose it such that it can be reached on the minikube host post 31999.
- In default namespace, run the pod testpod , based on busy bos image and using the sleep infiity commadn as its default command

- From the testpod Pod , use wget --spider --timeout=1 to verify you can access the default webpage of the remoteweb Pod
- Also verify that this web page is accessible on minkube host port 31000.

kubectl create -n remote
kubectl run pod remoteweb -n remote --image=nginx
kubectl expose pod remoteweb -n remote --port=80

THen edit remoteweb file
kubectl edit svc remotweb -n remote:

Add in nodeport = 3199
and then change type from ClusterIP to NodePort

Then test connection to port
find the minikube ip: 

curl the minikube ip : curl 192.168.49.2:3199 : returns port 80.



## Managing Incoming Traffic
## Understanding Ingress
- Ingress is used to provide external access to internal Kubernetes cluster resources
      - Ingress uses an external load balancer
      - This load balancer is implemented by the Ingress controller which is running as a Kubernetes application.
      - As an API resource, Ingress uses Service to connect to Pods that are used as a service endpoint.
      - To access resource in the cluster, the host name resolution must be configured to resolve the Ingress load balancer IP.

- Ingress controller is a Pod not a file
      - Ingress exposes HTTP and HTTPS routes from outside the cluster to Pods within the cluster.
      - Traffic routing is controlled by rules defined on the ingress resource.



## Installing Ecosystem Ingress Controllers
      - How to install ingress controller.

 helm upgrade --install ingress-nginx ingress-nginx --repo http://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace

controlplane ~ ➜  kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx-controller-6c657c6487-tbd4v   0/1     Running   0          18s

controlplane ~ ➜  kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx-controller-6c657c6487-tbd4v   1/1     Running   0          49s


## Using the Minikube Ingress Controller
- minikube is a Kubernetes distribution and comes with addons to integret thrid-part solutions
    - Use minikube addons list to show available addons
    - Use minikube addons enable to enable a specific addon


Minikube, add-ons are pre-packaged Kubernetes components and services that you can easily enable or disable to extend the functionality of your local Kubernetes cluster.

Add-ons let you:

Install common Kubernetes services without manual YAML files

Quickly set up development and testing environments

Enable features that are often needed in real clusters

The Ingress add-on in Minikube provides a ready-to-use Ingress Controller so you can route external HTTP/HTTPS traffic to services inside your Kubernetes cluster.

Traffic Flow :

Browser → Minikube IP → Ingress Controller → Service → Pod

minikube addons list : list addons

2. Enable ingress addons 
minikube addons enable ingress


*** If Pods will not start you need to check the kubectl scheduler Pod.

The Pod would not start, so i deleted all resources within in the ingress-nginx namespace

linux2@kubernetes:~$ kubectl -n ingress-nginx  delete all

Then sleep 10.

Re-ran the minikube enable addons ingress  : command.

The Pods came up successfully.

linux2@kubernetes:~$ kubectl -n ingress-nginx describe pod ingress-ng

ing). Image size: 323984203 bytes.
  Normal   Created      4m29s                kubelet                   Created container: controller
  Normal   Started      4m28s                kubelet                   Started container controller
  Normal   RELOAD       4m26s                nginx-ingress-controller  NGINX reload triggered due t


** Only the controller needs to be consistently running:

kubectl get all -n ingress-nginx
NAME                                           READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-tmxjg       0/1     Completed   0          8m5s
pod/ingress-nginx-admission-patch-s8n4l        0/1     Completed   0          8m4s
pod/ingress-nginx-controller-9cc49f96f-2rh7w   1/1     Running     0          8m5s

NAME                                         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    10.97.132.108   <none>        80:32360/TCP,443:32067/TCP   8m5s
service/ingress-nginx-controller-admission   ClusterIP   10.110.86.81    <none>        443/TCP                      8m5s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           8m5s

NAME                                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-9cc49f96f   1         1         1       8m5s

NAME                                       STATUS     COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   Complete   1/1           9s         8m5s
job.batch/ingress-nginx-admission-patch    Complete   1/1           8s         8m5s
linux2@kubernetes:~$ 


kubectl apply → API Server → Admission → Controller → Pods

Two different systems are involved:

Admission (webhooks) → decide if a request is allowed / modified

Before the Ingress object is stored:

Admission checks:

Is it valid?

Should defaults be added?

Is it allowed by policy?

-------------------------------
Controllers → make the cluster match the desired state

ou create a Deployment:

replicas: 3


Controller logic:

“I see only 1 Pod”

“I need 3 Pods”

Creates 2 more Pods

-----------------------------------

## Using Ingress
## DEMO ##

1. Help menu for creating ingress
kubectl create ingress -h
Create an ingress with the specified name.

2. We will create a rule that will forward ingress traffic to a service.

Created ingress service called nginxsvc

linux2@kubernetes:~$ kubectl create ingress nginxsvc --rule="/=nginxsvc:80"
ingress.networking.k8s.io/nginxsvc created
linux2@kubernetes:~$ 

This will route any ingress request for nginxsvc to port 80 .
nginxsvc is our minikube ip


3. linux2@kubernetes:~$ minikube ip
192.168.49.2

Add this ip to /etc/hosts

4. kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        14d
nginx        ClusterIP   10.104.68.158    <none>        80/TCP         2d16h
nginxsvc     NodePort    10.110.135.103   <none>        80:31303/TCP   4d


5. Describe ingress service, you can see the path of the rule we set for ingress service

linux2@kubernetes:~$ kubectl describe ingress nginxsvc
Name:             nginxsvc
Labels:           <none>
Namespace:        default
Address:          192.168.49.2
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /   nginxsvc:80 (10.244.0.32:80,10.244.0.31:80,10.244.0.35:80)
Annotations:  <none>
Events:
  Type    Reason  Age                    From                      Message
  ----    ------  ----                   ----                      -------
  Normal  Sync    3m58s (x2 over 4m56s)  nginx-ingress-controller  Scheduled for sync


6. Curl endpoint:

curl nginxsvc.info
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title

## Ingress Rules
- An optional host to be used as a name-based virtualhost. If no host is specified, the rule applies to all inbound HTTP traffic
- A list of paths (like/testpath). Each path has its own backend. Paths can be exposed as a regular expression
- The backend, which is a service resource. It is common to configure a default backend in an gress controller for incoming traffic that doesnt match a specific path.

The ingress pathType specifies how to deal with path requests
THe Exact pathType indicates that an exact match should occur
    - If the path is set to /foo and request is /foo/ there is no mathc
The Prefix pathType indicates that the requested path should start with
    - If the path is set /, any requested path will match
    - If the path is set /foo, /foo as well as /foo/ will match

## Ingress Types
- Ingress backend by a single Service : there is one that defines access to one backend Service
    - kubectl create ingress single --rule="/files=fileservice:80"

- Single fanout: there are two or more rules defining different paths that refer to different services
    - kubectl create ingress single --rule="/files=filesservice:80" -- rule="/db=dbservice:80"

- Name-baed virtual hosting: There are two or more rules that route requests baed on the host header,
** MAke sure dns entry for each host header
    - kubectl create ingress multihost --
    rule="my.example.com/files*=filesservice:80" -- rule="my.example.org/data*=dataservice:80"


When deleting namepace get stuck To remove a namespace that is stuck at "Terminating"

🚑 Force delete a stuck namespace (safe here)
Step 1: Get namespace JSON
kubectl get ns ingress-nginx -o json > ingress-nginx.json

Step 2: Remove finalizers

Edit the file:

nano ingress-nginx.json


Find this section:

"spec": {
  "finalizers": [
    "kubernetes"
  ]
}


Change it to:

"spec": {
  "finalizers": []
}


Save and exit.

Step 3: Force finalize deletion
kubectl replace --raw "/api/v1/namespaces/ingress-nginx/finalize" -f ingress-nginx.json

✅ Verify deletion
kubectl get ns ingress-nginx


You should now see:

Error from server (NotFound)

🧹 Extra cleanup (IMPORTANT for your earlier error)

Check for leftover webhook:

kubectl get validatingwebhookconfiguration | grep nginx


If you see:

ingress-nginx-admission


Delete it:

kubectl delete validatingwebhookconfiguration ingress-nginx-admission


This step prevents the webhook timeout error you hit earlier.



To fix deployment issue:
-----------------------

linux2@kubernetes:~$ kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx-controller-6c657c6487-bzw26   1/1     Running   0          72s
linux2@kubernetes:~$ minikube addons enable ingress
💡  ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/ingress-nginx/controller:v1.13.2
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.2
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.2
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled
linux2@kubernetes:~$ kubectl get pod -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-lntl7        0/1     Completed   0          77s
ingress-nginx-admission-patch-mlqpl         0/1     Completed   0          77s
ingress-nginx-controller-6db8d8cccd-rsdpg   1/1     Running     0          77s
linux2@kubernetes:~$ kubectl get all -n ingress-nginx
NAME                                            READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-lntl7        0/1     Completed   0          97s
pod/ingress-nginx-admission-patch-mlqpl         0/1     Completed   0          97s
pod/ingress-nginx-controller-6db8d8cccd-rsdpg   1/1     Running     0          97s

NAME                                         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    10.108.178.33   <none>        80:32721/TCP,443:32553/TCP   3m25s
service/ingress-nginx-controller-admission   ClusterIP   10.106.179.1    <none>        443/TCP                      3m25s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           3m26s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-6c657c6487   0         0         0       3m26s
replicaset.apps/ingress-nginx-controller-6db8d8cccd   1         1         1       98s

NAME                                       STATUS     COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   Complete   1/1           12s        98s
job.batch/ingress-nginx-admission-patch    Complete   1/1           11s        98s
linux2@kubernetes:~$ kubectl describe ingress nginxsvc
Error from server (NotFound): ingresses.networking.k8s.io "nginxsvc" not found
linux2@kubernetes:~$ kubectl create ingress nginxsvc --rule="/=nginxsvc:80"
ingress.networking.k8s.io/nginxsvc created
linux2@kubernetes:~$ kubectl describe ingress nginxsvc
Name:             nginxsvc
Labels:           <none>
Namespace:        default
Address:          
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /   nginxsvc:80 (<error: services "nginxsvc" not found>)
Annotations:  <none>
Events:
  Type    Reason  Age   From                      Message
  ----    ------  ----  ----                      -------
  Normal  Sync    4s    nginx-ingress-controller  Scheduled for sync
linux2@kubernetes:~$ minikube ip
192.168.49.2
linux2@kubernetes:~$ curl nginxsvc.info
<html>
<head><title>503 Service Temporarily Unavailable</title></head>
<body>
<center><h1>503 Service Temporarily Unavailable</h1></center>
<hr><center>nginx</center>
</body>
</html>
linux2@kubernetes:~$ kubectl describe ingress nginxsvc
Name:             nginxsvc
Labels:           <none>
Namespace:        default
Address:          192.168.49.2
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /   nginxsvc:80 (<error: services "nginxsvc" not found>)
Annotations:  <none>
Events:
  Type    Reason  Age                From                      Message
  ----    ------  ----               ----                      -------
  Normal  Sync    12s (x2 over 52s)  nginx-ingress-controller  Scheduled for sync
linux2@kubernetes:~$ kubectl create deployment nginxsvc --image=nginx
deployment.apps/nginxsvc created
linux2@kubernetes:~$ kubectl expose deployment nginxsvc --port=80 --target-port=80
service/nginxsvc exposed
linux2@kubernetes:~$ kubectl get svc nginxsvc
NAME       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
nginxsvc   ClusterIP   10.105.99.36   <none>        80/TCP    7s
linux2@kubernetes:~$ curl nginxsvc.info
<html>
<head><title>503 Service Temporarily Unavailable</title></head>
<body>
<center><h1>503 Service Temporarily Unavailable</h1></center>
<hr><center>nginx</center>
</body>
</html>
linux2@kubernetes:~$ kubectl edit svc nginxsvc
service/nginxsvc edited
linux2@kubernetes:~$ curl nginxsvc.info
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
linux2@kubernetes:~$ kubectl describe ingress nginxsvc
Name:             nginxsvc
Labels:           <none>
Namespace:        default
Address:          192.168.49.2
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *           
              /   nginxsvc:80 (10.244.0.12:80)
Annotations:  <none>
Events:
  Type    Reason  Age                   From                      Message
  ----    ------  ----                  ----                      -------
  Normal  Sync    7m1s (x2 over 7m41s)  nginx-ingress-controller  Scheduled for sync


------------------------------

## Demo: Name-baed Virtual Host
1. Create two deployments called saturn and mars. 
2. Expose both deployments on port 80
3. Add entries to /etc/hosts


linux2@kubernetes:~$ kubectl create deploy mars --image=nginx
deployment.apps/mars created
linux2@kubernetes:~$ kubectl create deploy saturn --image=nginx
deployment.apps/saturn created
linux2@kubernetes:~$ for i in mars saturn ; do kubectl expose deploy $i --port=80 ; done
service/mars exposed
service/saturn exposed
linux2@kubernetes:~$ minikube ip
192.168.49.2
linux2@kubernetes:~$ minikube ip >> /etc/hosts
bash: /etc/hosts: Permission denied
linux2@kubernetes:~$ sudo vi /etc/hosts

4. Test pining hostname
5. Once successfully pinging, Created an ingress rule for both saturn and mars each to match name mars and saturn respectfully.


kubectl create ingress multihost --rule="mars.example.com/=mars:80" --rule="saturn.example.com/=saturn:80"
ingress.networking.k8s.io/multihost created
linux2@kubernetes:~$ 

6. For this demo, we need to edit the config file and  change the Patch type from Exact to Prefix

Example :

- host: saturn.example.com
    http:
      paths:
      - backend:
          service:
            name: saturn
            port:
              number: 80
        path: /
        pathType: Prefix

linux2@kubernetes:~$ kubectl edit ingress multihost
ingress.networking.k8s.io/multihost edited
linux2@kubernetes:~$ 


curl mars
curl saturn

This should return a valid nginx endpoint




IF installation fails verify that helm package is installed. When you delete all resources all resources in namespace gets deleted. The helm provides a package that creates a service for the pods to run.


1, First install helm deployments,
2. Then enable minikube addons
3. If release is stuck in "Terminating", delete minkikube and start it again"


* Gateway API use specific API resources
    - To wor with Gateway API, A Gateway API controller needs to be installed.
    - You must install the helm package first.

* Gateway Class
    - The GatewayClass resource represents the physical Gateway Controller
        - Uses spec.controllerName to connect to a specific GatewayController
Once Gateway is required
    - Gateway uses gatewayClass Name property.
    - It also defines listerns to specify which protocols  should be serviced

Routes incoming traffic to be forwarded
    - Incoming requests identified by spec.hostnames
    - The parentRef property connects to HTTPRoute to a Gateway, then backendRefs property connects the HTTPRoute to a Service

### Demo Step to Creae a API Gateway"
* Understanding the Procedure
    1. Make sure the required custome resources are available

kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml
Warning: unrecognized format "int64"
customresourcedefinition.apiextensions.k8s.io/gatewayclasses.gateway.networking.k8s.io created
Warning: unrecognized format "int32"
customresourcedefinition.apiextensions.k8s.io/gateways.gateway.networking.k8s.io created
customresourcedefinition.apiextensions.k8s.io/httproutes.gateway.networking.k8s.io created
customresourcedefinition.apiextensions.k8s.io/referencegrants.gateway.networking.k8s.io created

----------

    2. Next, install a community Gateway API controller (Helm deployment)

helm install ngf oci://ghcr.io/nginxinc/charts/nginx-gateway-fabric --create-namespace -n nginx-gateway
Pulled: ghcr.io/nginxinc/charts/nginx-gateway-fabric:1.5.1
Digest: sha256:5a8e2fb46822c756dfe32af8bf9e09cd63808e4316b564c74c3d0afb3c828d45
I1226 13:08:21.958617  100262 warnings.go:110] "Warning: unrecognized format \"int32\""
I1226 13:08:21.959435  100262 warnings.go:110] "Warning: unrecognized format \"int64\""
I1226 13:08:22.100468  100262 warnings.go:110] "Warning: unrecognized format \"int64\""
I1226 13:08:22.134242  100262 warnings.go:110] "Warning: unrecognized format \"int32\""
I1226 13:08:22.257118  100262 warnings.go:110] "Warning: unrecognized format \"int32\""
I1226 13:08:22.257338  100262 warnings.go:110] "Warning: unrecognized format \"int64\""
I1226 13:08:22.322882  100262 warnings.go:110] "Warning: unrecognized format \"int64\""
NAME: ngf
LAST DEPLOYED: Fri Dec 26 13:08:22 2025
NAMESPACE: nginx-gateway
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
TEST SUITE: None


***
kubectl get pods,svc -n nginx-gateway
NAME                                            READY   STATUS             RESTARTS      AGE
pod/ngf-nginx-gateway-fabric-85df46c4fb-fsncv   1/2     CrashLoopBackOff   1 (16s ago)   86s

NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ngf-nginx-gateway-fabric   LoadBalancer   10.105.250.165   <pending>     80:30423/TCP,443:30909/TCP   86s
linux2@kubernetes:~$ kubectl logs -n nginx-gateway ngf-nginx-gateway-fabric-85df46c4fb-fsncv

"level":"error","ts":"2025-12-26T13:10:22Z","msg":"error received after stop sequence was engaged","error":"leader election lost","stacktrace":"sigs.k8s.io/controller-runtime/pkg/manager.(*controllerManager).engageStopProcedure.func1\n\tpkg/mod/sigs.k8s.io/controller-runtime@v0.19.1/pkg/manager/internal.go:512"}
failed to start control loop: failed to prepare the first batch: no matches for kind "GRPCRoute" in version "gateway.networking.k8s.io/v1"



This fails due to version being outdated

Find the latest version here:

https://github.com/kubernetes-sigs/gateway-api/releases


To fix, we need to install the new package version v1.4.1
Then delete the pod ngf-nginx-gateway pod
Then list pod and svc

kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.1/standard-install.yaml
Warning: unrecognized format "int64"
Warning: unrecognized format "int32"
customresourcedefinition.apiextensions.k8s.io/backendtlspolicies.gateway.networking.k8s.io created
customresourcedefinition.apiextensions.k8s.io/gatewayclasses.gateway.networking.k8s.io configured
customresourcedefinition.apiextensions.k8s.io/gateways.gateway.networking.k8s.io configured
customresourcedefinition.apiextensions.k8s.io/grpcroutes.gateway.networking.k8s.io created
customresourcedefinition.apiextensions.k8s.io/httproutes.gateway.networking.k8s.io configured
customresourcedefinition.apiextensions.k8s.io/referencegrants.gateway.networking.k8s.io configured
linux2@kubernetes:~$ kubectl get pods,svc -n nginx-gateway
NAME                                            READY   STATUS             RESTARTS        AGE
pod/ngf-nginx-gateway-fabric-85df46c4fb-fsncv   1/2     CrashLoopBackOff   6 (2m51s ago)   10m

NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ngf-nginx-gateway-fabric   LoadBalancer   10.105.250.165   <pending>     80:30423/TCP,443:30909/TCP   10m
linux2@kubernetes:~$ kubectl delete pod ngf-nginx-gateway-fabric-85df46c4fb-fsncv -n nginx-gateway
pod "ngf-nginx-gateway-fabric-85df46c4fb-fsncv" deleted
linux2@kubernetes:~$ kubectl get pods,svc -n nginx-gateway
NAME                                            READY   STATUS    RESTARTS   AGE
pod/ngf-nginx-gateway-fabric-85df46c4fb-zqn4l   2/2     Running   0          8s

NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ngf-nginx-gateway-fabric   LoadBalancer   10.105.250.165   <pending>     80:30423/TCP,443:30909/TCP   10m
linux2@kubernetes:~$ 

****
-------

kubectl edit -n nginx-gateway svc ngf-nginx-gateway-fabric
* Change the svc file from loadbalancer to NodePort

kubectl edit -n nginx-gateway svc ngf-nginx-gateway-fabric
service/ngf-nginx-gateway-fabric edited
linux2@kubernetes:~$ kubectl get pods,svc -n nginx-gateway
NAME                                            READY   STATUS    RESTARTS   AGE
pod/ngf-nginx-gateway-fabric-85df46c4fb-zqn4l   2/2     Running   0          6m43s

NAME                               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ngf-nginx-gateway-fabric   NodePort   10.105.250.165   <none>        80:30423/TCP,443:30909/TCP   17m



    3. Verify the community Gateway Controller is ready to accept incoming requests

## Using Gateway API

Create deployment and expose port 80

 kubectl create deployment nginxgw --image=nginx --replicas=3
deployment.apps/nginxgw created
linux2@kubernetes:~$ kubectl expose deploy nginxgw --port=80
service/nginxgw exposed

Then go to course file ckad > http-routing.yaml

inux2@kubernetes:~/ckad$ #Apply http-routing.yaml file
linux2@kubernetes:~/ckad$ kubectl apply -f http-routing.yaml
------------------
gateway.gateway.networking.k8s.io/example-gateway created
httproute.gateway.networking.k8s.io/example-route created
-----------
linux2@kubernetes:~/ckad$ # Addin localhost to resolve /etc/hosts
linux2@kubernetes:~/ckad$ sudo sh -c "echo 127.0.0.1 whatever.com >> /etc/hosts"

* Configuring Port Forwarding

linux2@kubernetes:~/ckad$ kubectl -n nginx-gateway port-forward ngf-nginx-gateway-fabric-85df46c4fb-zqn4l 8080:80 8443:443 &
[1] 128068

linux2@kubernetes:~/ckad$ Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
Forwarding from 127.0.0.1:8443 -> 443
Forwarding from [::1]:8443 -> 443

linux2@kubernetes:~/ckad$ curl whatever.com
curl: (7) Failed to connect to whatever.com port 80 after 1 ms: Couldn't connect to server
linux2@kubernetes:~/ckad$ curl whatever.com:8080
Handling connection for 8080
<!DOCTYPE html>
<html>
<head>

Edit the /etc/hosts for whatever.com and make it your minikube ip

kubectl get pods,svc -n nginx-gateway
NAME                                            READY   STATUS    RESTARTS   AGE
pod/ngf-nginx-gateway-fabric-85df46c4fb-zqn4l   2/2     Running   0          19m

NAME                               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
service/ngf-nginx-gateway-fabric   NodePort   10.105.250.165   <none>        80:30423/TCP,443:30909/TCP   3


Internally Port is 80: 30423 is our NodePort. 30423 is the port we can curl and will be able to listen on our gatewat service

linux2@kubernetes:~/ckad$ curl whatever.com:30423
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; 



    4. Create the Kubernetes application you want to provide access to.
    5. Configure GatewayClass, Gateway, and HTTPRoute
    6. Test by accessing the Service that exposes the community controller




## Troubleshooting Networking
kubectl get pods -o wide : shows Pod IP addresses
kubectl get svc shows Services
kubectl describe svc .. shows Service succesful connection to Pods
    - Check selector and label are matching
kubectl get netpol -A shows if any NetworkPolicies are operational
kubectl describe ing  : shows Ingress configuration: look for endpoints
    - If Ingress shows no endpoints, ensure the Service is uses does show them.


## Lesson 11 Lab : Managing Incoming Traffic
* Create a Deployment with the name "lab11web", based on the nginx container image and running 3 Pod instances.
* Make this Deployment accessible at the virtual hostname lab11web.example.com



linux2@kubernetes:~/ckad$ kubectl create deploy lab11web --image=nginx --replicas=3
deployment.apps/lab11web created
linux2@kubernetes:~/ckad$ kubectl get deploy,pods -A
NAMESPACE       NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
default         deployment.apps/lab11web                   3/3     3            3           19s
default         deployment.apps/mars                       1/1     1            1           96m
default         deployment.apps/nginxgw                    3/3     3            3           43m
default         deployment.apps/nginxsvc                   1/1     1            1           112m
default         deployment.apps/saturn                     1/1     1            1           96m
ingress-nginx   deployment.apps/ingress-nginx-controller   1/1     1            1           121m
kube-system     deployment.apps/coredns                    1/1     1            1           130m
nginx-gateway   deployment.apps/ngf-nginx-gateway-fabric   1/1     1            1           63m

NAMESPACE       NAME                                            READY   STATUS      RESTARTS       AGE
default         pod/lab11web-f99ffdf8f-4z89x                    1/1     Running     0              19s
default         pod/lab11web-f99ffdf8f-bmqb5                    1/1     Running     0              19s
default         pod/lab11web-f99ffdf8f-rkqtg                    1/1     Running     0              19s
default         pod/mars-7854db465-8r6q8                        1/1     Running     0              96m
default         pod/nginxgw-7f99f9f4d4-2zldz                    1/1     Running     0              43m
default         pod/nginxgw-7f99f9f4d4-5sl7c                    1/1     Running     0              43m
default         pod/nginxgw-7f99f9f4d4-rcgpv                    1/1     Running     0              43m
default         pod/nginxsvc-5985d79656-llgnf                   1/1     Running     0              112m
default         pod/saturn-8f9f6d467-s8ngh                      1/1     Running     0              96m
ingress-nginx   pod/ingress-nginx-admission-create-lntl7        0/1     Completed   0              119m
ingress-nginx   pod/ingress-nginx-admission-patch-mlqpl         0/1     Completed   0              119m
ingress-nginx   pod/ingress-nginx-controller-6db8d8cccd-rsdpg   1/1     Running     0              119m
kube-system     pod/coredns-66bc5c9577-hcftx                    1/1     Running     0              129m
kube-system     pod/etcd-minikube                               1/1     Running     0              130m
kube-system     pod/kube-apiserver-minikube                     1/1     Running     0              130m
kube-system     pod/kube-controller-manager-minikube            1/1     Running     0              130m
kube-system     pod/kube-proxy-bq5hz                            1/1     Running     0              130m
kube-system     pod/kube-scheduler-minikube                     1/1     Running     0              130m
kube-system     pod/storage-provisioner                         1/1     Running     1 (129m ago)   130m
nginx-gateway   pod/ngf-nginx-gateway-fabric-85df46c4fb-zqn4l   2/2     Running     0              52m


linux2@kubernetes:~/ckad$ kubectl get deploy
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
lab11web   3/3     3            3           33s
mars       1/1     1            1           96m
nginxgw    3/3     3            3           43m
nginxsvc   1/1     1            1           113m
saturn     1/1     1            1           96m
linux2@kubernetes:~/ckad$ kubectl get deploy,pods | grep "lab11web"
deployment.apps/lab11web   3/3     3            3           65s
pod/lab11web-f99ffdf8f-4z89x    1/1     Running   0          65s
pod/lab11web-f99ffdf8f-bmqb5    1/1     Running   0          65s
pod/lab11web-f99ffdf8f-rkqtg    1/1     Running   0          65s
linux2@kubernetes:~/ckad$ kubectl expose deploy lab11web --port=80
service/lab11web exposed

--
inux2@kubernetes:~/ckad$ kubectl create ingress multihost-lab11 --rule="lab11web.example.com/=lab11web:80"
ingress.networking.k8s.io/multihost-lab11 created
linux2@kubernetes:~/ckad$ kubectl edit ing multihost-lab11
ingress.networking.k8s.io/multihost-lab11 edited
linux2@kubernetes:~/ckad$ curl lab11web.example.com
<!DOCTYPE html>
<html>
<head>

--

kubectl get ing
NAME              CLASS   HOSTS                                 ADDRESS        PORTS   AGE
multihost         nginx   mars.example.com,saturn.example.com   192.168.49.2   80      105m
multihost-lab11   nginx   lab11web.example.com                  192.168.49.2   80      4m33s
nginxsvc          nginx   *                                     192.168.49.2   80      126m
linux2@kubernetes:~/ckad$ kubectl describe ing multihost-lab11
Name:             multihost-lab11
Labels:           <none>
Namespace:        default
Address:          192.168.49.2
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host                  Path  Backends
  ----                  ----  --------
  lab11web.example.com  
                        /   lab11web:80 (10.244.0.20:80,10.244.0.22:80,10.244.0.21:80)
Annotations:            <none>
Events:
  Type    Reason  Age                    From                      Message
  ----    ------  ----                   ----                      -------
  Normal  Sync    3m48s (x3 over 4m48s)  nginx-ingress-controller  Scheduled for sync

** Inside of this log file :

kubectl explain ingress.spec : You will find resource pages and man pages


# Modeule 5 : Application Environment Configuratiom and Security
## ConfigMaps and Secrets

* A ConfigMap in Kubernetes is an object used to store non-secret configuration data (settings, files, environment values) separately from application code.
    - Settings file for your app that kubernetes manages. Stored outside your container.

* Provide Variables to Kubernetes Application
 - To run a varaible in a Deployment use the set command when running.
1. First, use kubectl create deploy mydb --image=mariadb
2. Next, use kubectl set env deploy mydb MYSQL_ROOT_PASSWORD=password

- When running a Pod, environment variables can be provided, but shouldnt run naked Pods
    - kubectl run mydb --image=mysql -- env="MYSQL_ROOT_PASSWORD=password"

## Demo: Generating a YAML File with Variables
kubectl create deploy mydb --image=mariadb

linux2@kubernetes:~$ kubectl describe pod mydb-6447978996-6sxvw
Name:             mydb-6447978996-6sxvw
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Sat, 27 Dec 2025 14:05:10 +0000
Labels:           app=mydb
                  pod-template-hash=6447978996

kubectl logs mydb-6447978996-6sxvw
2025-12-27 14:05:13+00:00 [Note] [Entrypoint]: Entrypoint script for MariaDB Server 1:12.1.2+maria~ubu2404 started.
2025-12-27 14:05:13+00:00 [Warn] [Entrypoint]: /sys/fs/cgroup///memory.pressure not writable, functionality unavailable to MariaDB


kubectl set env deploy mydb MARIADB_ROOT_PASSWORD=password


linux2@kubernetes:~$ kubectl get deploy mydb -o yaml > mariadb_deploy.yaml
linux2@kubernetes:~$ kubectl get deploy mydb
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
mydb   1/1     1            1           30m
linux2@kubernetes:~$ kubectl delete deploy mydb
deployment.apps "mydb" deleted
linux2@kubernetes:~$ kubectl apply -f mariadb
mariadb2.yaml   mariadb_deploy  mariadb.yaml    
linux2@kubernetes:~$ kubectl apply -f mariadb_deploy ^C
linux2@kubernetes:~$ mv mariadb_deploy mariadb_deploy.yaml
linux2@kubernetes:~$ kubectl apply -f mariadb_deploy.yaml 
deployment.apps/mydb created

------------------
## Providing Variables with ConfigMaps 
* Understanding ConfigMaps
    - The ConfigMap is an API resource to store specific information
        - Two different uses: Variable storage and Config files storage up to a size of 1MiB
    - If bigger amounts of data are need , they should be stored in a Pod Volume

Use kubectl create cm to create a ConfigMap,
    --from-literal key=value
    --from-env-file=/path/to/file

An environment file is a file that has multiple varaibles defined on different lines
Add --dry-run=client -o yaml to generate YAML code instead of creating the resources

The easy way to use variables from ConfigMaps using kubectl set env
    - kubectl set env --from=configmap /mycm deploy/mydeploy
    - While using kubectl set env, the --prefix option can be used to put a prefix before the variables as defined in the ConfigMap

## DEMO: Working with ConfigMaps ##
Config Maps is like a secrets manager that stores variables


ux2@kubernetes:~$ kubectl create deploy mydb --image=mariadb --replicas=3
deployment.apps/mydb created
linux2@kubernetes:~$ kubectl create cm mydbvars --from-literal=ROOT_PASSWORD=password
configmap/mydbvars created
linux2@kubernetes:~$ kubectl get cm mydbvars -o yaml
apiVersion: v1
data:
  ROOT_PASSWORD: password
kind: ConfigMap
metadata:
  creationTimestamp: "2025-12-27T18:03:49Z"
  name: mydbvars
  namespace: default
  resourceVersion: "16145"
  uid: 64c001f3-ff7f-4163-8055-e4f1b4d21460
linux2@kubernetes:~$ kubectl set env deploy/mydbbb --from configmap/mydbvars --prefix=MARIADB_
Error from server (NotFound): deployments.apps "mydbbb" not found
linux2@kubernetes:~$ kubectl set env deploy/mydb --from configmap/mydbvars --prefix=MARIADB_
deployment.apps/mydb env updated
linux2@kubernetes:~$ kubectl get deploy mydb -o yaml | grep env -A 5
      - env:
        - name: MARIADB_ROOT_PASSWORD
          valueFrom:
            configMapKeyRef:
              key: ROOT_PASSWORD
              name: mydbvars
linux2@kubernetes:~$ #The end this above shows how the password is reference in the configMAp
linux2@kubernetes:~$ exit


## Using Configuration Files
    - ConfipMap can contain one or more configuration files.
    - In the data section of the ConfigMap, each file is referred to with its own key
    - To use configuration files from ConfigMaps, the Config needs to be used as a Pod volume and mounted on a directory.

# Creating index file
linux2@kubernetes:~$ echo hello world > index.html
linux2@kubernetes:~$ # Creating ConfigMap myindex
linux2@kubernetes:~$ kubectl create cm myindex --from-file=index.html
configmap/myindex created
linux2@kubernetes:~$ kubectl get cm --show-labels=myindex
error: invalid argument "myindex" for "--show-labels" flag: strconv.ParseBool: parsing "myindex": invalid syntax
See 'kubectl get --help' for usage.
linux2@kubernetes:~$ kubectl get cm --selector=myindex
No resources found in default namespace.
linux2@kubernetes:~$ kubectl get cm -A
NAMESPACE         NAME                                                   DATA   AGE
default           kube-root-ca.crt                                       1      30h
default           mydbvars                                               1      18m
default           myindex                                                1      42s
ingress-nginx     ingress-nginx-controller                               1      30h
ingress-nginx     kube-root-ca.crt                                       1      30h
ingress-nginx     tcp-services                                           0      30h
ingress-nginx     udp-services                                           0      30h
kube-node-lease   kube-root-ca.crt                                       1      30h
kube-public       cluster-info                                           1      30h
kube-public       kube-root-ca.crt                                       1      30h
kube-system       coredns                                                1      30h
kube-system       extension-apiserver-authentication                     6      30h
kube-system       kube-apiserver-legacy-service-account-token-tracking   1      30h
kube-system       kube-proxy                                             2      30h
kube-system       kube-root-ca.crt                                       1      30h
kube-system       kubeadm-config                                         1      30h
kube-system       kubelet-config                                         1      30h
nginx-gateway     kube-root-ca.crt                                       1      29h
nginx-gateway     nginx-includes-bootstrap                               1      29h
linux2@kubernetes:~$ kubectl describe cm myindex
Name:         myindex
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
index.html:
----
hello world



BinaryData
====

Events:  <none>
linux2@kubernetes:~$ # Create Deployment for myweb
linux2@kubernetes:~$ kubectl create deploy myweb --image=nginx
deployment.apps/myweb created
linux2@kubernetes:~$ # Edit deployment apps for myweb
linux2@kubernetes:~$ kubectl edit deployments.app

## Secrets
Understanding Secrets
    - A secret is a base-64 encoded alternative for a ConfigMap
        Secret types used for tpyical scenarios
            generic: used for generic sensitive values like password
            tls: stores TLS keys

## Configure Applications to use Secrets
Different use cases for using secrets in applications
    - To provide TLS keys to the application: kubectl create secret tls my-tls-keys --cert=tls/my.crt --key=tls/my.key
    - To provide security to passwords: kubectl create secret generic my-secret-pw -- from-literal=password=verysecret
    - To provide access to an SSH private key: kubectl create secret generic my-ssh-key --from-file=ssh-private-key=.ssh/id_rsa
    - To provide acces to sensitive files, which would be mounted in the application with root access only: kubectl create secret generic my-secret-file --from-file=/my/secretfile

If it contains variables, use kubectl set env 
If it contains files, mount the secret.
    - While mounting the secret in pod spec use defaultMode
    - Notice that mounted secrets are automatically updated in the application when the secret is updated.

Secrets are stored in etcd, on kube user has access to that account

How to create a secret.

linux2@kubernetes:~$ kubectl create secret generic dbpw --from-literal=ROOT_PASSWORD=password
secret/dbpw created
linux2@kubernetes:~$ kubectl describe secret dbpw
Name:         dbpw
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
ROOT_PASSWORD:  8 bytes


linux2@kubernetes:~$ kubectl get secret dbpw -o yaml
apiVersion: v1
data:
  ROOT_PASSWORD: cGFzc3dvcmQ=
kind: Secret
metadata:
  creationTimestamp: "2025-12-28T02:24:52Z"
  name: dbpw
  namespace: default
  resourceVersion: "21360"
  uid: 0023521b-a72b-4b70-acae-891a3c851e5a
type: Opaque
linux2@kubernetes:~$ echo cGFzc3dvcmQ= | base64 -d
passwordlinux2@kubernetes:~$

The prefix that you use will be shown in the image, so for example mariadb has a prefix for password for MYSQL_



inux2@kubernetes:~$ kubectl set env deploy mynewdb --from=secret/dbpw --prefix=MYSQL_
deployment.apps/mynewdb env updated
linux2@kubernetes:~$ kubectl get deploy mynewdb -o yaml | grep secret
            secretKeyRef:
linux2@kubernetes:~$ kubectl get deploy mynewdb -o yaml | less



Inside of the yaml file you should see the reference of the password you set

spec:
      containers:
      - env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              key: ROOT_PASSWORD
              name: dbpw

## Using Secrets for Registry Access
- Container registries require authenticated access.
  - To provide the access credentials, they can be stored in the docker-registry Secret type
  - While creating a docker-registry type Secret.
      - Specify all required values on the command line
      - Import the values from the config.json file that has been generated by logging in from the command line.
  - To use docker-registry tpe secrets, the pod.spec.image.PullSecrets key is used.


### Lesson 12 Lab : Using ConfigMaps and Secrets
* Create a Deployment with the name "secretlab" which runs the mariadb image
* Create a Secret with the name "supersecret" that sets the variable ROOT_PASSWORD to the value password
* Ensure the secretlab Deployment uses this secret to provide the variable MARIADB_ROOT_PASSWORD to the application.


Solution:

linux2@kubernetes:~/Desktop$ kubectl create secret generic supersecret --from-literal=ROOT_PASSWORD=password
secret/supersecret created
linux2@kubernetes:~/Desktop$ kubectl describe secret supersecret
Name:         supersecret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
ROOT_PASSWORD:  8 bytes
linux2@kubernetes:~/Desktop$ kubectl get secret supersecret -o yaml










apiVersion: v1
data:
  ROOT_PASSWORD: cGFzc3dvcmQ=
kind: Secret
metadata:
  creationTimestamp: "2025-12-28T15:04:59Z"
  name: supersecret
  namespace: default
  resourceVersion: "22420"
  uid: 730899f6-4abf-4e2c-b985-fc6a7791cdab
type: Opaque

linux2@kubernetes:~/Desktop$ echo "cGFzc3dvcmQ=" | base64 -d
passwordlinux2@kubernetes:~/Desktop$ kubectl get deploy | grep mariadb
linux2@kubernetes:~/Desktop$ kubectl get deploy | grep secretlab
linux2@kubernetes:~/Desktop$ kubectl get deploy -A



linux2@kubernetes:~/Desktop$ kubectl create deploy secretlab --image=mariadb
deployment.apps/secretlab created


linux2@kubernetes:~/Desktop$ kubectl set env --from=secret/supersecret deployment/secretlab --prefix=MARIADB_
deployment.apps/secretlab env updated


Verify Pod and Deployment is running :

linux2@kubernetes:~/Desktop$ kubectl get all --selector app=secretlab
NAME                             READY   STATUS    RESTARTS   AGE
pod/secretlab-86c8658db4-nvfs7   1/1     Running   0          3m23s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/secretlab   1/1     1            1           34m

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/secretlab-5545bc8d5    0         0         0       20m
replicaset.apps/secretlab-5fc59db4fc   0         0         0       28m
replicaset.apps/secretlab-78d5969779   0         0         0       34m
replicaset.apps/secretlab-7f6c5cdd66   0         0         0       23m
replicaset.apps/secretlab-7fcc5cfd74   0         0         0       21m
replicaset.apps/secretlab-85ff877678   0         0         0       21m
replicaset.apps/secretlab-86c8658db4   1         1         1       3m23s


# Deploying Applications the DevOps Way
- GitOps operator picks up changes and applies them to Kubernetes cluster in an automated way

## Blue/ Green Deployment

Create a deployment which is your blue deployment.
Then once completed, You need to add your app to etc hosts file
After that deploy your green deployment. Once app is successful. THen which it over in your deployment 
  kubectl edit ing myapp.

Then scale dwon blue deployment

kubectl scale deploy blue-deploy --replicas=0

## Demo : Ingress Based Blue/Green Deployments Ingress ##


Part 1: Creating the Blue application
  cd ~/ckad/kustomize-bluegreen/blue 
  cat files inside dir
  kubectl apply -k .
  kubectl get deploy,pods,svc,cm,ing

Part 2: Testing application access
sudo sh -c "echo $(minikube ip) myapp.local >> /etc/hosts
In browser: http://myapp.local


Part 3 : Creating the Green application
cd ~/ckad/kustomize-bluegreen/green
cat *
kubectl apply -k .

Part 4: Making the Switch
sed -i -e 's/blue-svc/green-svc/'myapp-ing.yaml
kubectl apply -f myapp-ing.yaml
curl http://myapp.local
kubectl deploy blue-deploy --replicas=0

### Demo: Service Based Blue/Green Deployment ###

linux2@kubernetes:~$ cd ~/ckad/
linux2@kubernetes:~/ckad$ cd kustomize-bluegreen/
linux2@kubernetes:~/ckad/kustomize-bluegreen$ ls
blue  green
linux2@kubernetes:~/ckad/kustomize-bluegreen$ cd green/
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl create deploy blue-nginx --image=nginx:1.14 --replicas=3
deployment.apps/blue-nginx created
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl expose deploy blue-nginx --port=80 --name=bgnginx
service/bgnginx exposed
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl get deploy blue-nginx -o yaml > green-nginx.yaml
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ # Modify every occurance from blue to green in green-nginx.yaml
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ 
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ vi green-nginx.yaml 
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl create -f green-nginx.yaml
deployment.apps/green-nginx created
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl create get pods 
error: Unexpected args: [get pods]
See 'kubectl create -h' for help and examples
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl get pods 
NAME                           READY   STATUS    RESTARTS      AGE
blue-nginx-cd95759fc-5vfxd     1/1     Running   0             4m58s
blue-nginx-cd95759fc-9zp74     1/1     Running   0             4m58s
blue-nginx-cd95759fc-dzb5l     1/1     Running   0             4m58s
green-nginx-7d94b99d94-62j5f   1/1     Running   0             63s
green-nginx-7d94b99d94-7w429   1/1     Running   0             63s
green-nginx-7d94b99d94-pc8cq   1/1     Running   0             63s
lab11web-f99ffdf8f-4z89x       1/1     Running   3 (22h ago)   2d22h
lab11web-f99ffdf8f-bmqb5       1/1     Running   3 (22h ago)   2d22h
lab11web-f99ffdf8f-rkqtg       1/1     Running   3 (22h ago)   2d22h
mars-7854db465-8r6q8           1/1     Running   3             3d
mydb-5777b67687-2s5tb          1/1     Running   2 (22h ago)   43h
mydb-5777b67687-5bxbd          1/1     Running   2 (22h ago)   43h
mydb-5777b67687-cwdwp          1/1     Running   2 (22h ago)   43h
mynewdb-746cb84dd6-fnvz5       1/1     Running   1 (22h ago)   34h
nginxgw-7f99f9f4d4-2zldz       1/1     Running   3 (22h ago)   2d23h
nginxgw-7f99f9f4d4-5sl7c       1/1     Running   3 (22h ago)   2d23h
nginxgw-7f99f9f4d4-rcgpv       1/1     Running   3 (22h ago)   2d23h
nginxsvc-5985d79656-llgnf      1/1     Running   3 (22h ago)   3d
saturn-8f9f6d467-s8ngh         1/1     Running   3 (22h ago)   3d
secretlab-86c8658db4-nvfs7     1/1     Running   0             21h
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
bgnginx      ClusterIP   10.107.86.104    <none>        80/TCP         4m39s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        3d1h
lab11web     ClusterIP   10.111.97.161    <none>        80/TCP         2d22h
mars         ClusterIP   10.105.78.46     <none>        80/TCP         3d
nginxgw      ClusterIP   10.102.192.91    <none>        80/TCP         2^[[I^[[2;2R^[[3;1R^[[>61;7600;1c^[]10;rgb:ffff/ffff/ffff^G^[]11;rgb:3030/0a0a/2424^G^[P1$r0 q^[\^[[?12;4$yd23h
nginxsvc     NodePort    10.105.99.36     <none>        80:30639/TCP   3d
saturn       ClusterIP   10.105.193.168   <none>        80/TCP         3d
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl delete svc bgnginx; kubectl expose deploy green-nginx --port=80 --name=bgnginx
service "bgnginx" deleted
service/bgnginx exposed
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl get pods 
NAME                           READY   STATUS    RESTARTS      AGE
blue-nginx-cd95759fc-5vfxd     1/1     Running   0             6m52s
blue-nginx-cd95759fc-9zp74     1/1     Running   0             6m52s
blue-nginx-cd95759fc-dzb5l     1/1     Running   0             6m52s
green-nginx-7d94b99d94-62j5f   1/1     Running   0             2m57s
green-nginx-7d94b99d94-7w429   1/1     Running   0             2m57s
green-nginx-7d94b99d94-pc8cq   1/1     Running   0             2m57s
lab11web-f99ffdf8f-4z89x       1/1     Running   3 (22h ago)   2d23h
lab11web-f99ffdf8f-bmqb5       1/1     Running   3 (22h ago)   2d23h
lab11web-f99ffdf8f-rkqtg       1/1     Running   3 (22h ago)   2d23h
mars-7854db465-8r6q8           1/1     Running   3             3d
mydb-5777b67687-2s5tb          1/1     Running   2 (22h ago)   43h
mydb-5777b67687-5bxbd          1/1     Running   2 (22h ago)   43h
mydb-5777b67687-cwdwp          1/1     Running   2 (22h ago)   43h
mynewdb-746cb84dd6-fnvz5       1/1     Running   1 (22h ago)   34h
nginxgw-7f99f9f4d4-2zldz       1/1     Running   3 (22h ago)   2d23h
nginxgw-7f99f9f4d4-5sl7c       1/1     Running   3 (22h ago)   2d23h
nginxgw-7f99f9f4d4-rcgpv       1/1     Running   3 (22h ago)   2d23h
nginxsvc-5985d79656-llgnf      1/1     Running   3 (22h ago)   3d
saturn-8f9f6d467-s8ngh         1/1     Running   3 (22h ago)   3d
secretlab-86c8658db4-nvfs7     1/1     Running   0             21h
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
bgnginx      ClusterIP   10.100.13.183    <none>        80/TCP         31s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        3d1h
lab11web     ClusterIP   10.111.97.161    <none>        80/TCP         2d22h
mars         ClusterIP   10.105.78.46     <none>        80/TCP         3d
nginxgw      ClusterIP   10.102.192.91    <none>        80/TCP         2d23h
nginxsvc     NodePort    10.105.99.36     <none>        80:30639/TCP   3d
saturn       ClusterIP   10.105.193.168   <none>        80/TCP         3d
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl delete deployments.apps blue-nginx
deployment.apps "blue-nginx" deleted
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
bgnginx      ClusterIP   10.100.13.183    <none>        80/TCP         62s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        3d1h
lab11web     ClusterIP   10.111.97.161    <none>        80/TCP         2d23h
mars         ClusterIP   10.105.78.46     <none>        80/TCP         3d
nginxgw      ClusterIP   10.102.192.91    <none>        80/TCP         2d23h
nginxsvc     NodePort    10.105.99.36     <none>        80:30639/TCP   3d
saturn       ClusterIP   10.105.193.168   <none>        80/TCP         3d


linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ kubectl get pods 
NAME                           READY   STATUS    RESTARTS      AGE
green-nginx-7d94b99d94-62j5f   1/1     Running   0             4m1s
green-nginx-7d94b99d94-7w429   1/1     Running   0             4m1s
green-nginx-7d94b99d94-pc8cq   1/1     Running   0             4m1s
lab11web-f99ffdf8f-4z89x       1/1     Running   3 (22h ago)   2d23h
lab11web-f99ffdf8f-bmqb5       1/1     Running   3 (22h ago)   2d23h
lab11web-f99ffdf8f-rkqtg       1/1     Running   3 (22h ago)   2d23h
mars-7854db465-8r6q8           1/1     Running   3             3d
mydb-5777b67687-2s5tb          1/1     Running   2 (22h ago)   43h
mydb-5777b67687-5bxbd          1/1     Running   2 (22h ago)   43h
mydb-5777b67687-cwdwp          1/1     Running   2 (22h ago)   43h
mynewdb-746cb84dd6-fnvz5       1/1     Running   1 (22h ago)   34h
nginxgw-7f99f9f4d4-2zldz       1/1     Running   3 (22h ago)   2d23h
nginxgw-7f99f9f4d4-5sl7c       1/1     Running   3 (22h ago)   2d23h
nginxgw-7f99f9f4d4-rcgpv       1/1     Running   3 (22h ago)   2d23h
nginxsvc-5985d79656-llgnf      1/1     Running   3 (22h ago)   3d
saturn-8f9f6d467-s8ngh         1/1     Running   3 (22h ago)   3d
secretlab-86c8658db4-nvfs7     1/1     Running   0             21h
linux2@kubernetes:~/ckad/kustomize-bluegreen/green$ #Officially shift traffic over to green

-----------------------------------------

## Canary Deployments
- A Canary Deployment upgrade strategy will expose a new version of the application to a limited number of users before completing the migration to the new version
  - This allows user exposure with minimized risk
  - Preferred to use ingress. Ingress-based Canary deployments are using two ingress resources pointing to the same Ingress virtual host

## Demo Canary Deployments ##

Lesson 13 Lab : Canary Deployments
- Run a Deployment with the name oldbird, based on the nginx:1.18
- Run a Deployment with the name "newbird", based on the nginx:latest image
- Expose this Deployment using a NodePorn Service in such a way that approximately 90% of the traffic is going to be directed to the old nginx version, and 10% will be directed to the new nginx version.


1. Create deployment with oldbird output to old.yaml file
  - Edit the old.yaml file and  add in the label type: bird under the app section.

  linux2@kubernetes:~$ kubectl create deploy oldbird --image=nginx:1.18 --dry-run=client -o yaml > old.yaml
linux2@kubernetes:~$ vi old.yaml 

linux2@kubernetes:~$ kubectl apply -f old.yaml
deployment.apps/oldbird created
linux2@kubernetes:~$ 


linux2@kubernetes:~$ kubectl get all --selector app=oldbird
NAME                           READY   STATUS    RESTARTS   AGE
pod/oldbird-669d8f9cdc-mphdl   1/1     Running   0          42s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/oldbird   1/1     1            1           43s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/oldbird-669d8f9cdc   1         1         1       43s



2. Create deployment with new out to new.yaml. Change old to new in the new.yaml file.

 kubectl create deploy oldbird --image=nginx:latest --dry-run=client -o yaml > new.yaml
linux2@kubernetes:~$ vi new.yaml 
linux2@kubernetes:~$ 

Apply new app

linux2@kubernetes:~$ kubectl get all --selector app=newbird
NAME                           READY   STATUS    RESTARTS   AGE
pod/newbird-7cd6dffd88-h4gq9   1/1     Running   0          17s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/newbird   1/1     1            1           17s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/newbird-7cd6dffd88   1         1         1       17s
linux2@kubernetes:~$ 

3. At this point we should have the New and Old Bird Deployment deployed.




