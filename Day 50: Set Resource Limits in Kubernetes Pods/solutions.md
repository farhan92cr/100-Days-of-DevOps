Step 1: Connect to Jump Host
bash
ssh thor@jump_host
Password: mjolnir123

Step 2: Create the Pod Definition with Resource Limits
Create a YAML file for the pod with resource requests and limits:

bash
cat > httpd-pod.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"
      limits:
        memory: "20Mi"
        cpu: "100m"
EOF
Step 3: Apply the Pod Configuration
Create the pod in the Kubernetes cluster:

bash
kubectl apply -f httpd-pod.yaml
Step 4: Verify the Pod was Created
Check that the pod is running:

bash
kubectl get pods
You should see output similar to:

text
NAME        READY   STATUS    RESTARTS   AGE
httpd-pod   1/1     Running   0          30s
Step 5: Verify Resource Limits
Check the pod details to verify the resource limits are set correctly:

bash
kubectl describe pod httpd-pod
Look for the Limits and Requests sections in the output.

Step 6: Verify Resource Specifications
You can also check the resource specifications directly:

bash
kubectl get pod httpd-pod -o jsonpath='{.spec.containers[0].resources}' | jq .
If jq is not available, use:

bash
kubectl get pod httpd-pod -o jsonpath='{.spec.containers[0].resources}'
Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to jump_host
ssh thor@jump_host

# Create the pod definition file with resource limits
cat > httpd-pod.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd-container
    image: httpd:latest
    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"
      limits:
        memory: "20Mi"
        cpu: "100m"
EOF

# Apply the pod configuration
kubectl apply -f httpd-pod.yaml

# Verify pod creation
kubectl get pods

# Verify resource limits in detail
kubectl describe pod httpd-pod

# Verify resource specifications
kubectl get pod httpd-pod -o jsonpath='{.spec.containers[0].resources}'
Alternative Verification Methods:
For more detailed verification, you can use:

bash
# Get complete pod definition in YAML format
kubectl get pod httpd-pod -o yaml

# Check specifically for resources section
kubectl get pod httpd-pod -o yaml | grep -A 10 resources

# Check pod events to see if there are any resource-related issues
kubectl get events --field-selector involvedObject.name=httpd-pod
Expected Results:
After running the commands, you should see:

Pod Status: httpd-pod should be in Running state

Container Name: Container named httpd-container

Image: Using httpd:latest image

Resource Requests:

Memory: 15Mi

CPU: 100m

Resource Limits:

Memory: 20Mi

CPU: 100m

Understanding Resource Units:
Memory: Mi means Mebibytes (1 Mi = 2^20 bytes)

CPU: 100m means 100 millicpu (0.1 CPU core, or 10% of a single CPU core)

Important Notes:
Requests: The minimum resources the container needs to run

Limits: The maximum resources the container can use

If a container exceeds its memory limit, it may be terminated

If a container exceeds its CPU limit, it will be throttled

The pod httpd-pod is now successfully created with the specified resource limits, which will help manage performance and prevent resource contention in the Kubernetes cluster.

