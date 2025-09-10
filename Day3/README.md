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

