# Day 3

## Lab - Creating an external NodePort service for nginx deployment
```
oc project jegan
oc get deploy
```

Delete your cluster-ip service
```
oc get svc
oc delete svc/nginx
```

Create the nodeport external service
```
oc expose deploy/nginx --type=NodePort --port=8080
oc get svc
oc describe svc/nginx
```

Accessing nodeport service
```
curl http://master01.ocp4.palmeto.org:32100
curl http://master02.ocp4.palmeto.org:32100
curl http://master03.ocp4.palmeto.org:32100
curl http://worker01.ocp4.palmeto.org:32100
curl http://worker02.ocp4.palmeto.org:32100
curl http://worker03.ocp4.palmeto.org:32100
```

<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/f08884cb-35a8-4149-9d8e-71d27aee71a4" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/c36bd777-7b67-4242-82da-1d707253a9c3" />

#### Things to note
<pre>
- Kubernetes/Openshift reserves 32000 to 32767 ports for the use of NodePort services
- for each NodePort service we create in K8s/Openshift, we end up opening one port in the range 32000-32767 on all the nodes in your cluster, this leads to security issues
- also, the number of master/workers nodes in dev/qa/prod environment would vary,and the IP address/hostnames of nodes could also vary, hence it is not a scalable/practical approach( not user-friendly )
- openshift offers a better solution for this, we could create a clusterip service and expose a route using the clusterip service, that way there won't be any security issues
</pre>

## Lab - Creating an external route for nginx deployment
Note
<pre>
- Openshift route feature underhood makes use of Kubernetes Ingress
- K8s Ingress is not a service, instead it is a routing rules
- For Ingress to work in your cluster, 3 components are required
  - Ingress ( Your forwarding rules )
  - Ingress Controller ( HAProxy/Nginx/F5 Ingress Controller )
  - Load Balancer ( HAProxy/Nginx/F5/etc )
  - assume the home page of my bank is https://www.abcbank.com
  - the abc bank has the below links/features in their website
    - login ( microservice ) - https://www.abcbank.com/login
    - balance enquiry ( microservice ) - https://www.abcbank.com/balance-enquiry
    - fund transfer ( microservice ) - https://www.abcbank.com/fund-transfer
    - cheque book request ( microservice ) - https://www.abcbank.com/cheque-request
    - personal loan (microservice) - https://www.abcbank.com/personal-loan
    - car loan ( microservice ) - https://www.abcbank.com/car-loan
    - logout ( microservice ) - https://www.abcbank.com/logout
</pre>

```
oc project jegan
oc get deploy
# The command below assumes you have a service 
oc expose svc/nginx
oc get route

oc describe route/nginx
```

## Lab - Deploying nginx using declarative style (using yaml manifest file)
```
oc project jegan
oc delete deploy/nginx svc/nginx route/nginx

oc create deploy nginx --image=image-registry.openshift-image-registry.svc:5000/openshift/nginx:1.27 --replicas=3 --dry-run=client -o json

oc create deploy nginx --image=image-registry.openshift-image-registry.svc:5000/openshift/nginx:1.27 --replicas=3 --dry-run=client -o yaml
oc create deploy nginx --image=image-registry.openshift-image-registry.svc:5000/openshift/nginx:1.27 --replicas=3 --dry-run=client -o yaml > nginx-deploy.yml

# This will assume the nginx deploy doesn't exist in your project namespace already, otherwise it will report errors
oc create -f nginx-deploy.yml --save-config

oc get deploy,rs,po
```

## Lab - Scale up nginx deployment in declarative style
```
oc project jegan
oc get pods -l app=nginx
cat nginx-deploy.yml
#Edit nginx-deploy.yml and update replicas=3 to replicas=5, save and apply the changes as shown below
cat nginx-deploy.yml

oc apply -f nginx-deploy.yml
oc get pods -l app=nginx
```

## Lab - Creating a clusterip service in declarative style
```
oc project jegan
oc get deploy
oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml
oc expose deploy/nginx --type=ClusterIP --port=8080 --dry-run=client -o yaml > nginx-clusterip-svc.yml
oc apply -f nginx-clusterip-svc.yml
oc get svc
```
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/a9c8cddb-4928-4925-aaeb-7ed47e862a10" />

## Lab - Creating a nodeport service in declarative style
Delete the clusterip service before proceeding
```
oc delete -f nginx-clusterip-svc.yml
```

Now you may create the nodeport service
```
oc project jegan
oc get deploy
oc expose deploy/nginx --type=NodePort --port=8080 --dry-run=client -o yaml
oc expose deploy/nginx --type=NodePort --port=8080 --dry-run=client -o yaml > nginx-nodeport-svc.yml
oc apply -f nginx-nodeport-svc.yml
oc get svc
```

## Lab - Creating a loadbalancer service in declarative style
Delete the nodeport service before proceeding
```
oc delete -f nginx-nodeport-svc.yml
```

Now you may create the loadbalancer service
```
oc project jegan
oc get deploy
oc expose deploy/nginx --type=LoadBalancer --port=8080 --dry-run=client -o yaml
oc expose deploy/nginx --type=LoadBalancer --port=8080 --dry-run=client -o yaml > nginx-lb-svc.yml
oc apply -f nginx-lb-svc.yml
oc get svc
```

## Lab - Rolling update
```
# Assuming you have deploy 3 pods of image version 1.27
oc apply -f nginx-deploy.yml
oc get pods -o yaml | grep image

# Edit the nginx-deploy.yml, update the image version from 1.27 to 1.28
oc apply -f nginx-deploy.yml
oc get pods -o yaml | grep image

# Check the status of rolling update
oc rollout status deploy/nginx

# Rolling back to previous version
oc rollout undo deploy/nginx

# Check the revision history
oc rollout history deploy/nginx
```

## Info - Persistent Volume(PV)
<pre>
- is an external storage
- this is created explicitly by the administrator
- PVs can be created manually or dynamically using StorageClass
- PV storage can come from NFS, third storage clusters in your on-prem environment or ebs/s3 buckets from AWS, similar storage solutions from public cloud, etc.,
- PV will have the following fields
  - size in MB/GB/TBs
  - storageclass ( optional )
  - accessMode
    - readWriteOnce ( all pods from the same node can have read and write access to this storage)
    - readWriteMany ( all pods from all nodes in the cluster will have read and write access go the storage )
</pre>  

## Info - Persistent Volume Claim (PVC)
<pre>
- Any application that needs external storage, they will have to express their requirement by way of PVC
- PVC wil have the following fields
  - size in MB/GB/TBs
  - storageclass ( optional )
  - storage type
  - accessMode
    - readWriteOnce ( all pods from the same node can have read and write access to this storage)
    - readWriteMany ( all pods from all nodes in the cluster will have read and write access go the storage ) 
- the Deployment/StatefulSet that needs external storage will express their requirement with the help of PVC
- openshift storage controller will scan thru the cluster looking for a matching PV to let the PVC claim and use it
</pre>

## Info - StorageClass
<pre>
- is a way the Persistent Volumes(PV) can be provisioned dynamically(runtime) on demand
- whenever a PVC is created in the cluster mentioning a storageclass name, the matching storageclass will provision the PV and let the application use that external storage
- storage class can be implemented for NFS, AWS elastic block storage or AWS S3 buckets, Azure Storage, etc.,
</pre>

## Lab - Deploying wordpress and mysql multi-pod application
In case you haven't cloned TekTutor training repository already, you may do now.  You need to update the below files replacing 'jegan' with your name.  Also you need to update the nfs server IP to 192.168.10.201 in case you are working in Server 2.  Also ensure the mysql and wordpress image version is 13.0 in case of server 2.

<pre>
mysql-pv.yml
mysql-pvc.yml
mysql-deploy.yml
wordpress-pv.yml 
wordpress-pvc.yml 
wordpress-deploy.yml 
</pre>
```
cd ~
git clone https://github.com/tektutor/openshift-sep-2025.git
cd openshift-sep-2025/Day3/declarative-manifest-scripts/wordpress
chmod +x ./deploy.sh ./undeploy.sh
./deploy.sh
```

Expected output
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/4c3b7e07-f1de-4be4-ae52-8286b350fd37" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/7005701f-4da7-4d89-80c0-dfc1fe6ca445" />
<img width="1920" height="1168" alt="image" src="https://github.com/user-attachments/assets/6003c771-88e8-4da7-a3b1-20d9bc11a270" />
