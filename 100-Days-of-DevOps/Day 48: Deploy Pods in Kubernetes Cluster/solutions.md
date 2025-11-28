Step 1: Connect to Jump Host
bash
ssh thor@jump_host
Password: mjolnir123

Step 2: Create the Pod Definition
Create a YAML file for the pod:

bash
cat > pod-httpd.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
EOF
Step 3: Apply the Pod Configuration
Create the pod in the Kubernetes cluster:

bash
kubectl apply -f pod-httpd.yaml
Step 4: Verify the Pod was Created
Check that the pod is running:

bash
kubectl get pods
You should see output similar to:

text
NAME        READY   STATUS    RESTARTS   AGE
pod-httpd   1/1     Running   0          30s
Step 5: Verify Pod Details
Check the pod details to ensure all specifications are correct:

bash
kubectl describe pod pod-httpd
Step 6: Verify Labels and Container Name
Check the labels and container name specifically:

bash
# Check labels
kubectl get pods --show-labels | grep pod-httpd

# Check container name
kubectl get pod pod-httpd -o jsonpath='{.spec.containers[*].name}'
Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to jump_host
ssh thor@jump_host

# Create the pod definition file
cat > pod-httpd.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
EOF

# Apply the pod configuration
kubectl apply -f pod-httpd.yaml

# Verify pod creation
kubectl get pods

# Verify pod details
kubectl describe pod pod-httpd

# Verify labels
kubectl get pods --show-labels | grep pod-httpd

# Verify container name
kubectl get pod pod-httpd -o jsonpath='{.spec.containers[*].name}' && echo
Alternative One-Line Command:
You can also create the pod directly without a YAML file:

bash
kubectl run pod-httpd --image=httpd:latest --labels="app=httpd_app" --container="httpd-container"
Expected Results:
After running the commands, you should see:

Pod Status: pod-httpd should be in Running state

Labels: The pod should have label app=httpd_app

Container Name: The container inside the pod should be named httpd-container

Image: The container should be using httpd:latest image

Verification Commands:
You can also use these commands for more detailed verification:

bash
# Check pod in different output formats
kubectl get pod pod-httpd -o wide
kubectl get pod pod-httpd -o yaml

# Check pod events
kubectl get events --field-selector involvedObject.name=pod-httpd

# Check pod logs (once it's running)
kubectl logs pod-httpd
Cleanup (Optional):
If you need to delete the pod and recreate it:

bash
kubectl delete pod pod-httpd
The pod pod-httpd is now successfully created in the Kubernetes cluster with all the required specifications:

Pod name: pod-httpd

Image: httpd:latest

Label: app=httpd_app

Container name: httpd-container


