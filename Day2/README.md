# Day 2

## Info - Container Orchestration Platform Overview
<pre>
- provides an eco system to make your applications highly-available
- it supports inbuilt monitoring features 
  - to check the health of your applications
  - repair faulty applications by restarting
  - moving applications instances running on unreachable machine with the container orchestration platform
  - self-healing platform
  - it can check whether your application is live (liveliness ), readiness( is it ready to serve user traffic )
- it allows us to scale up/down based on user traffic to your application
- it supports rolling update
  - upgrading your live application from one version to the other without any downtime
  - also supports roll back
- allows exposing your application for internal only consumption or optionally to external world using service abstraction
- supports service discovery
  - is a way your application can be accessed using service name in the place of IP address
- some orchestration platform also supports user management
  - Red Hat Openshift supports user management
- examples
  - Docker SWARM
  - Google Kubernetes
  - Red Hat Openshift
</pre>

## Info - Docker SWARM
<pre>
- native Orchestration Platform from Docker Inc organization
- this container orchestration platform only supports docker containerized application workloads
- it is lightweight, hence can be installed including laptops with average/normal configuration
- easy to install/deploy/learn
- used in Dev/QA environment
- ideal for learning
- not production grade
</pre>

## Info - Kubernetes
<pre>
- this is developed by Google in Go lang
- it supports many different types of containerized worloads
- any Container Runtime/Engine that implements CRI are supported by Kubernetes
- supports deploying rootless and rootful container images
- no user-management
- supports only command-line
- opensource, hence no support provided
- managed Kubernetes services 
  - AWS - EKS ( Elastic Kubernetes Service - you get support from Amazon )
  - Azure - AKS ( Azure Kubernetes Service - you get support from Microsoft )
- it is robust and production grade
- supports extending the Kubernetes API/Features by creating Custom Resource Defintions ( CRD )
- supports Operators
  - it is combination of many Custom Resources and Custom Controllers
- applications are deployed as something called Pod
- Pod 
  - is a collection of related containers
  - technically any number of containers can be there in a Pod
  - IP address is assigned on the pod level not on the container level
  - Port Range 0-65535 is available on the Pod level, hence all containers within the Pod has a common port range on the Pod level
  - Assume Pod P1 has Containers C1 and C2, if Container C1 is using port 8000 then Container C2 won't be able to use it as it is already taken by Container C1
</pre>

## Info - Red Hat Openshift
<pre>
- it is developed on top of opensource Google Kubernetes
- it is superset of Kubernetes
- in otherwords, it is Red Hat's Distribution of Kubernetes with many additional useful features
- it supports command-line and webconsole
- it comes with in-built performance metrics ( Prometheus and Grafan pre-integrated )
- it has an internal image registry ( comes out of the box unlike Kubernetes )
- supports user management
  - We could create multiple users, teams, with specific permission and access restrictions
- Using the Kubernetes CRD and Operators, Red Hat was able to support many additional features on top of Kubernetes
</pre>

## Info - Red Hat Openshift High-Level Architecture
![Openshift](openshiftArchitecture.png)

## Info - Control Plane Components
<pre>
- Control Plane Components are the heart of Kubernetes/Openshift container orchestration platform
  1. API Server
  2. etcd key/value database
  3. Controller Managers
  4. Scheduler
</pre>

#### API Server
<pre>
- this is the brain of Kubernetes/Openshift
- is a Pod
- is supports set of REST APIs for all the container Orchestration features supported by Openshift
- API Server is the only components that has write access to etcd database
- all the Kubernetes/Openshift components are allowed to talk to only API Server
- the K8s/Openshift components talk to API Server via REST calls only
- the API Server responds back via events only
- each the API Server updates the etcd database, it sends broadcasting events
</pre>

#### etcd database
<pre>
- this is an independent opensouce key/value database used by Kubernetes and Openshift
- is a Pod
- it is a distributed database that normally works as a cluster of many etcd database server instances
- they are designed to work as a cluster, and they know how to synchronize data when they are in the same cluster
- to form a minimal cluster 3 nodes are required, hence in Openshift 3 masters are mandatory
</pre>

#### Controller Managers
<pre>
- it is a collection of many Controllers
  - is a Pod
  - Deployment Controller
  - ReplicaSet Controller
  - StatefulSet Controller
  - DaemonSet Controller
  - Job Controller
  - CronJob Controller
  - Node Controller
  - Endpoint Controller
</pre>


#### Scheduler
<pre>
- is a Pod
- this component is responsible to identify healthy nodes where user applications can be deployed
- the scheduling recommendataions are shared by Scheduler to the API Server via REST call
</pre>

## Info - Pod
<pre>
- is a logical grouping of many containers
- applications runs inside containers
- every Pod has one pause container and one main application container
- technically a Pod may have any number of containers
- as a best-practice, there should be only one main application container per Pod
- however, a Pod may have some support containers a.k.a side-car containers optionally
- Pod is a configuration (YAML document) which is stored inside etcd database by API Server
</pre>

## Info - ReplicaSet
<pre>
- is a Kubernetes/Openshift resource/object
- it captures the desired number of Pods instances that be running,
- it also captures how many Pods are currently running
- it also captures how many Pods are ready to server user traffic
- the container image that must be used to deploy your application
- in short, it is a configuration (YAML document) which is stored inside etcd database by API Server
</pre>

## Info - Deployment
<pre>
- is a Kubernetes/Openshift resource/object
- it represents your application
- it captures the name of your application 
- it captures the desired number of Pods that must be created
- the container image that must be used to deploy your application
- it is used to deploy stateless application
</pre>

## Info - Deployment Controller
<pre>
- it is a Pod that is part of Controller Managers ( one of the Control Plane Component )
- manages Deployment Resource
- it constantly looks for ( it gets notified by API Server events related to Deployment )
  - new deployment created
  - deployment edited/deleted
  - deployment scaled up/down
- this controller used Deployment as the input
</pre>

## Lab - Check your lab environment for Openshift
```
oc version
kubectl version
oc get nodes
oc get nodes -o wide
```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/b67eddb5-d246-4017-bc97-37132baf17eb" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/a509f045-695a-4339-8055-1c6f1e788bad" />

## Lab - Finding more details about nodes
```
oc describe node/master01.ocp4.palmeto.org
oc describe node/master02.ocp4.palmeto.org
oc describe node/master03.ocp4.palmeto.org
oc describe node/worker01.ocp4.palmeto.org
oc describe node/worker02.ocp4.palmeto.org
oc describe node/worker03.ocp4.palmeto.org

```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/a2bad996-3e25-45a0-9b83-76203c253e0c" />

## Lab - Creating a project in your name
```
oc new-project jegan
```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/bf06e435-13b8-4c09-93c8-e5c346f6c7e0" />


