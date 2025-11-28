Step 1: Connect to Jump Host
bash
ssh thor@jump_host
Password: mjolnir123

Step 2: Check Current Deployment Status
First, let's check the current state of the deployment:

bash
kubectl get deployments
kubectl get pods -l app=nginx
Step 3: Check Rollout History
View the rollout history to see available revisions:

bash
kubectl rollout history deployment/nginx-deployment
Step 4: Get Detailed History Information
For more detailed history including change causes:

bash
kubectl rollout history deployment/nginx-deployment --revision=<revision-number>
Or to see all revisions in detail:

bash
kubectl get replicasets -l app=nginx
Step 5: Perform the Rollback
Rollback to the previous revision:

bash
kubectl rollout undo deployment/nginx-deployment
Step 6: Monitor the Rollback Process
Monitor the rollback status:

bash
kubectl rollout status deployment/nginx-deployment
Step 7: Verify the Rollback was Successful
Check that the deployment is now using the previous version:

bash
kubectl get deployment nginx-deployment -o jsonpath='{.spec.template.spec.containers[0].image}' && echo


