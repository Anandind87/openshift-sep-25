# Day 4

## Info - Securing Openshift Applications
<pre>
- Use Role-Based Access Control (RBAC)
  - Define roles and permissions for users and service accounts
  - Grant the least privilege necessary
- Network Policies
  - Use Openshift Network Policies to restrict pod-to-pod and pod external communications
- Secure Secrets Managements
  - Store sensitive data (passwords, tokens) in Openshift Secrets, Hashicorp vault, etc.,
  - Avoid hardcoding secrets in images or environment variables
- TLS 
  - use HTTPs for all routes
  - enable TLS between services when possible
- Image Security
  - Use trusted, signed and regularly updated container images
  - Scan images for vulnerabilities
- Pod Security
  - Use Security Context Contraints (SCC) to limit pod privileges
  - Avoid running containers are root
- Resource Quotas and Limits
  - Set resource quotas and limits to prevent resource exhaustion
- Audit and Logging
  - Enable audit logging for cluster and application events
  - Monitor logs for suspicious activity
- Regular updates
  - keep openshift and applicaiton dependencies up to date
- Automated Testing
  - Integrate security testing into your CI/CD pipeline
</pre>

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
