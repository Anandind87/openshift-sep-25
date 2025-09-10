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
curl http://master01.ocp4.palmeto.org
curl http://master02.ocp4.palmeto.org
curl http://master03.ocp4.palmeto.org
curl http://worker01.ocp4.palmeto.org
curl http://worker02.ocp4.palmeto.org
curl http://worker03.ocp4.palmeto.org
```
