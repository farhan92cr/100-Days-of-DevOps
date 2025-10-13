Here's the complete set of commands to run:

bash
# Connect to jump_host
ssh thor@jump_host

# Create the deployment definition file
cat > httpd-deployment.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd
spec:
  selector:
    matchLabels:
      app: httpd
  replicas: 1
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - name: httpd-container
        image: httpd:latest
EOF

# Apply the deployment configuration
kubectl apply -f httpd-deployment.yaml

# Verify deployment creation
kubectl get deployments

# Verify pods created by the deployment
kubectl get pods

# Verify deployment details
kubectl describe deployment httpd
Alternative One-Line Command:
You can also create the deployment directly without a YAML file:

bash
kubectl create deployment httpd --image=httpd:latest
However, using the YAML file gives you more control over the specifications.

Additional Verification Commands:
You can use these commands for more detailed verification:

bash
# Check deployment in different output formats
kubectl get deployment httpd -o wide
kubectl get deployment httpd -o yaml

# Check the replica set created by the deployment
kubectl get replicasets

# Check deployment rollout status
kubectl rollout status deployment/httpd

# Check pod details
kubectl get pods -l app=httpd
Expected Results:
After running the commands, you should see:

Deployment Status: httpd deployment should show 1/1 ready replicas

Pod Status: At least one pod should be running with name pattern httpd-xxxxxxxxxx-xxxxx

Image: The pod should be using httpd:latest image

Labels: The pods should have label app=httpd

Key Differences from Pod (Day 48):
Deployment manages ReplicaSets and provides rolling updates

Deployment ensures the desired number of pods are running

Deployment provides self-healing capabilities

Deployment allows for easy scaling and updates

The deployment httpd is now successfully created in the Kubernetes cluster and will maintain one running instance of the httpd application using the httpd:latest image.