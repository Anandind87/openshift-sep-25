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
