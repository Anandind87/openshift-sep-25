# Day 4

## Lab - Scheduler affinity

#### Preferred
```
cd ~/openshift-sep-2025
git pull
cd Day4/scheduler-node-affinity

# List and see if there are any nodes that has label disk=sdd, expectation is none
oc get nodes -l disk=ssd

oc apply -f nginx-preferred-deploy.yml

# You may notice, the pods still got deployed though scheduler couldn't find
# nodes that meet the criteria
oc get pods -o wide
oc get pods -o wide

# Let's delete the deployment
oc delete -f nginx-preferred-deploy.yml

# Let's label one of the node with disk=ssd label
oc label node/worker03.ocp4.palmeto.org disk=ssd
oc get nodes -l disk=ssd

#Now, let's deploy
oc apply -f nginx-preferred-deploy.yml

#You should see all pods deployed into worker03
oc get pods -o wide

# Let's delete this and try out the required scheduler affinity
oc delete -f nginx-preferred-deploy.yml

# Worker03 has disk type sdd
oc apply -f nginx-required-deploy.yml

#You should see all pods deployed into worker03
oc get pods -o wide

# Let's delete this and try out the same when no node meets the criteria
oc delete -f nginx-preferred-deploy.yml
oc label node/worker03.ocp4.palmeto.org disk-
# Worker03 has disk type sdd
oc apply -f nginx-required-deploy.yml

#You should see all pods in the pending state
oc get pods -o wide
```
