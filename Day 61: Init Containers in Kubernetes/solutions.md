What are Init Containers?
Init Containers are special containers that run before the main application containers in a Pod. They are designed to perform setup tasks that need to be completed before your main application starts.
  for info see the notes.md file:


Step 1: Check Existing Resources
bash
# Check if there are any existing deployments with similar names
kubectl get deployments

# Check if there are any existing pods with similar names
kubectl get pods
Step 2: Create the Deployment with Init Containers
Based on the requirements, here's the deployment configuration:

bash
cat > ic-deploy-nautilus.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-nautilus
  template:
    metadata:
      labels:
        app: ic-nautilus
    spec:
      initContainers:
      - name: ic-msg-nautilus
        image: ubuntu:latest
        command: ['/bin/bash', '-c', 'echo Init Done - Welcome to xFusionCorp Industries > /ic/media']
        volumeMounts:
        - name: ic-volume-nautilus
          mountPath: /ic
      containers:
      - name: ic-main-nautilus
        image: ubuntu:latest
        command: ['/bin/bash', '-c', 'while true; do cat /ic/media; sleep 5; done']
        volumeMounts:
        - name: ic-volume-nautilus
          mountPath: /ic
      volumes:
      - name: ic-volume-nautilus
        emptyDir: {}
EOF
Step 3: Apply the Deployment
bash
kubectl apply -f ic-deploy-nautilus.yaml
Step 4: Verify the Deployment
bash
# Check deployment status
kubectl get deployment ic-deploy-nautilus

# Check pod status (wait for init container to complete)
kubectl get pods -l app=ic-nautilus

# Check pod details to see init container status
kubectl describe pods -l app=ic-nautilus
Step 5: Verify Init Container Worked
bash
# Check logs from the main container to see the output
kubectl logs -l app=ic-nautilus -c ic-main-nautilus

# You should see "Init Done - Welcome to xFusionCorp Industries" repeated every 5 seconds
Complete Solution:
Run these commands in sequence:

bash
# 1. Create the deployment YAML
cat > ic-deploy-nautilus.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ic-deploy-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-nautilus
  template:
    metadata:
      labels:
        app: ic-nautilus
    spec:
      initContainers:
      - name: ic-msg-nautilus
        image: ubuntu:latest
        command: ['/bin/bash', '-c', 'echo Init Done - Welcome to xFusionCorp Industries > /ic/media']
        volumeMounts:
        - name: ic-volume-nautilus
          mountPath: /ic
      containers:
      - name: ic-main-nautilus
        image: ubuntu:latest
        command: ['/bin/bash', '-c', 'while true; do cat /ic/media; sleep 5; done']
        volumeMounts:
        - name: ic-volume-nautilus
          mountPath: /ic
      volumes:
      - name: ic-volume-nautilus
        emptyDir: {}
EOF

# 2. Apply the deployment
kubectl apply -f ic-deploy-nautilus.yaml

# 3. Wait for deployment to be ready
kubectl rollout status deployment/ic-deploy-nautilus --timeout=60s

# 4. Check the pod status
kubectl get pods -l app=ic-nautilus

# 5. Check the logs to verify init container worked
echo "=== Checking Main Container Logs ==="
kubectl logs -l app=ic-nautilus -c ic-main-nautilus --tail=10
Expected Result:
The init container runs first and creates a file /ic/media with the message

The main container then runs and continuously reads and displays the file content every 5 seconds

You should see "Init Done - Welcome to xFusionCorp Industries" in the logs repeatedly

What Init Containers Do:
Run before the main application containers

Can perform setup tasks, configuration, or dependency checks

Share volumes with main containers

Must complete successfully before main containers start

Please run the first command to check for any existing resources, then we can proceed with creating the deployment.


