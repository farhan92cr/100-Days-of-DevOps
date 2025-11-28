Step 1: Create the Deployment
bash
cat > nginx-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
EOF
Step 2: Create the NodePort Service
bash
cat > nginx-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30011
EOF
Step 3: Apply the Configuration
bash
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml
Step 4: Verify the Deployment
bash
kubectl get deployments
kubectl get pods -l app=nginx
Step 5: Verify the Service
bash
kubectl get services
kubectl describe service nginx-service
Complete Solution Commands:
bash
# 1. Create deployment YAML
cat > nginx-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
EOF

# 2. Create service YAML
cat > nginx-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30011
EOF

# 3. Apply configurations
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-service.yaml

# 4. Verify deployment
echo "=== Deployment Status ==="
kubectl get deployment nginx-deployment

echo "=== Pods Status ==="
kubectl get pods -l app=nginx

# 5. Verify service
echo "=== Service Status ==="
kubectl get service nginx-service

# 6. Detailed verification
echo "=== Service Details ==="
kubectl describe service nginx-service

echo "=== Pod Details ==="
kubectl describe pods -l app=nginx
Alternative: Single File Approach
You can also create both in one file:

bash
cat > nginx-app.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30011
EOF

kubectl apply -f nginx-app.yaml
Verification and Testing:
bash
# Test the service (from jump_host)
curl http://localhost:30011/

# Or if you want to test from outside, get the node IP
kubectl get nodes -o wide

# Check rollout status
kubectl rollout status deployment/nginx-deployment

# Check replica sets
kubectl get replicasets
Expected Results:
Deployment: nginx-deployment with 3 replicas

Pods: 3 pods named nginx-deployment-xxxxx with container nginx-container

Service: nginx-service of type NodePort on port 30011

High Availability: If one pod fails, the deployment automatically creates a new one

Scalability: Easy to scale up/down by changing replica count

Access: Website accessible via http://<node-ip>:30011/

Key Benefits:
High Availability: 3 replicas ensure service continuity

Load Distribution: Service distributes traffic across all pods

External Access: NodePort allows external access to the application

Easy Scaling: Can easily scale replicas up or down as needed

The nginx web server is now deployed in a highly available and scalable configuration on the Kubernetes cluster!

