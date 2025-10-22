Step 1: Create the Grafana Deployment
bash
cat > grafana-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana-container
        image: grafana/grafana:latest
        ports:
        - containerPort: 3000
        env:
        - name: GF_SECURITY_ADMIN_PASSWORD
          value: "admin123"
EOF
Step 2: Create the NodePort Service
bash
cat > grafana-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 32000
EOF
Step 3: Apply the Configuration
bash
kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml
Step 4: Verify the Deployment
bash
kubectl get deployments
kubectl get pods -l app=grafana
Step 5: Verify the Service
bash
kubectl get services
kubectl describe service grafana-service
Complete Solution Commands:
bash
# 1. Create Grafana deployment YAML
cat > grafana-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana-container
        image: grafana/grafana:latest
        ports:
        - containerPort: 3000
        env:
        - name: GF_SECURITY_ADMIN_PASSWORD
          value: "admin123"
EOF

# 2. Create NodePort service YAML
cat > grafana-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 32000
EOF

# 3. Apply configurations
kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml

# 4. Wait for deployment to be ready
echo "=== Waiting for Grafana to be ready ==="
kubectl wait --for=condition=ready pod -l app=grafana --timeout=120s

# 5. Verify deployment
echo "=== Deployment Status ==="
kubectl get deployment grafana-deployment-nautilus

echo "=== Pods Status ==="
kubectl get pods -l app=grafana

# 6. Verify service
echo "=== Service Status ==="
kubectl get service grafana-service

# 7. Check pod logs to ensure Grafana started properly
echo "=== Checking Grafana Logs ==="
kubectl logs -l app=grafana --tail=10
Alternative: Single File Approach
bash
cat > grafana-app.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-nautilus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana-container
        image: grafana/grafana:latest
        ports:
        - containerPort: 3000
        env:
        - name: GF_SECURITY_ADMIN_PASSWORD
          value: "admin123"
---
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 32000
EOF

kubectl apply -f grafana-app.yaml
Testing and Verification:
bash
# Test the Grafana service (from jump_host)
curl -I http://localhost:32000/

# Or get the node IP for external access
kubectl get nodes -o wide

# Check if Grafana is responding properly
curl http://localhost:32000/api/health

# Monitor pod startup
kubectl logs -l app=grafana -f
Accessing Grafana:
Once deployed, you can access Grafana by:

URL: http://<node-ip>:32000/

Username: admin (default)

Password: admin123 (as set in the deployment)

Expected Results:
Deployment: grafana-deployment-nautilus with 1 replica

Pod: Running Grafana container on port 3000

Service: grafana-service of type NodePort on port 32000

Access: Grafana login page accessible via port 32000

Default Credentials: admin/admin123

Key Features:
High Availability: Can scale replicas if needed

External Access: NodePort allows access from outside the cluster

Security: Admin password set via environment variable

Standard Port: Grafana runs on its default port 3000 internally

The Grafana deployment is now ready and accessible on port 32000!


