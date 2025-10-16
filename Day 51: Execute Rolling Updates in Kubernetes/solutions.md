Step 1: Connect to Jump Host
bash
ssh thor@jump_host
Password: mjolnir123

Step 2: Check Current Deployment Status
First, let's check the current state of the deployment:

bash
kubectl get deployments
kubectl get pods -l app=nginx
Step 3: Check Current Image Version
Verify the current image being used by the deployment:

bash
kubectl describe deployment nginx-deployment
Or more specifically:

bash
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[0].image}'
Step 4: Perform the Rolling Update
Update the deployment to use the new nginx:1.18 image:

bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.18
Step 5: Monitor the Rolling Update
Monitor the rollout status to ensure it completes successfully:

bash
kubectl rollout status deployment/nginx-deployment
Step 6: Verify the Update was Successful
Check that the deployment is now using the new image:

bash
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[0].image}'
You should see: nginx:1.18

Step 7: Verify All Pods are Operational
Check that all pods are running with the new image:

bash
kubectl get pods -l app=nginx
Check the image version of the running pods:

bash
kubectl get pods -l app=nginx -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to jump_host
ssh thor@jump_host

# Check current deployment status
kubectl get deployments
kubectl get pods -l app=nginx

# Check current image version
kubectl describe deployment nginx-deployment

# Perform the rolling update
kubectl set image deployment/nginx-deployment nginx=nginx:1.18

# Monitor the rollout
kubectl rollout status deployment/nginx-deployment

# Verify the new image is being used
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[0].image}' && echo

# Verify all pods are operational
kubectl get pods -l app=nginx
kubectl get pods -l app=nginx -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[0].image}{"\n"}{end}'
Alternative Method: Using Patch Command
If you prefer, you can also use the patch command:

bash
kubectl patch deployment nginx-deployment -p '{"spec":{"template":{"spec":{"containers":[{"name":"nginx","image":"nginx:1.18"}]}}}}'
Additional Verification Commands:
For more detailed verification:

bash
# Check deployment rollout history
kubectl rollout history deployment/nginx-deployment

# Check replica sets (you should see a new replica set for the update)
kubectl get replicasets

# Describe the deployment to see events
kubectl describe deployment nginx-deployment

# Check pod status and readiness
kubectl get pods -l app=nginx -o wide
Expected Results:
After the rolling update:

Deployment Status: nginx-deployment should show all replicas ready

Pod Status: All pods should be in Running state

Image Version: All pods should be using nginx:1.18 image

Rollout Status: The rollout should complete without errors

Zero Downtime: The application should remain available during the update

Rolling Update Characteristics:
Gradual Update: Pods are updated one by one

Zero Downtime: Application remains available during update

Rollback Capability: You can rollback if needed using kubectl rollout undo

Health Checks: Kubernetes ensures new pods are healthy before terminating old ones

Troubleshooting:
If the rollout fails or gets stuck:

bash
# Check rollout status in detail
kubectl rollout status deployment/nginx-deployment --watch

# Check pod events for any issues
kubectl get events --sort-by=.metadata.creationTimestamp

# Rollback if necessary
kubectl rollout undo deployment/nginx-deployment
The rolling update has now been successfully performed, and all pods should be operational with the new nginx:1.18 image.





The container name in the deployment is not "nginx". Let me check what the actual container name is and then perform the update.

Step 1: Check the Current Deployment Details
bash
kubectl describe deployment nginx-deployment
Step 2: Find the Correct Container Name
bash
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[*].name}'
Step 3: Check Current Image
bash
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[0].image}'
Step 4: Perform the Rolling Update with Correct Container Name
Based on the container name found in step 2, run the update:

bash
# If the container name is different, use the correct name
kubectl set image deployment/nginx-deployment <container-name>=nginx:1.18

