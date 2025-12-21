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

