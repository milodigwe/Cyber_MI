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