# Understanding and Using Containers

https://github.com/sandervanvugt/ckad 
Github repo

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



