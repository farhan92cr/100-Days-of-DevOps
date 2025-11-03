Step 1: Create Redis Master Deployment and Service
bash
# Redis Master Deployment
cat > redis-master-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
  labels:
    app: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - name: master-redis-devops
        image: redis
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
EOF

# Redis Master Service
cat > redis-master-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: redis-master
  labels:
    app: redis-master
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis-master
EOF
Step 2: Create Redis Slave Deployment and Service
bash
# Redis Slave Deployment
cat > redis-slave-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
  labels:
    app: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - name: slave-redis-devops
        image: gcr.io/google_samples/gb-redisslave:v3
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
EOF

# Redis Slave Service
cat > redis-slave-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis-slave
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis-slave
EOF
Step 3: Create Frontend Deployment and Service
bash
# Frontend Deployment
cat > frontend-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: php-redis-devops
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
EOF

# Frontend Service (NodePort)
cat > frontend-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30009
  selector:
    app: frontend
EOF
Complete Solution in Single File:
bash
# Create all resources in one file
cat > guestbook-complete.yaml << 'EOF'
# Redis Master Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
  labels:
    app: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - name: master-redis-devops
        image: redis
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
---
# Redis Master Service
apiVersion: v1
kind: Service
metadata:
  name: redis-master
  labels:
    app: redis-master
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis-master
---
# Redis Slave Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
  labels:
    app: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - name: slave-redis-devops
        image: gcr.io/google_samples/gb-redisslave:v3
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
---
# Redis Slave Service
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis-slave
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis-slave
---
# Frontend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: php-redis-devops
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
---
# Frontend Service
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30009
  selector:
    app: frontend
EOF

# Apply everything at once
kubectl apply -f guestbook-complete.yaml
Step 4: Apply All Configurations
bash
# Apply all configurations
kubectl apply -f redis-master-deployment.yaml
kubectl apply -f redis-master-service.yaml
kubectl apply -f redis-slave-deployment.yaml
kubectl apply -f redis-slave-service.yaml
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml
Step 5: Verify Everything is Running
bash
# Wait for all deployments to be ready
kubectl rollout status deployment/redis-master --timeout=120s
kubectl rollout status deployment/redis-slave --timeout=120s
kubectl rollout status deployment/frontend --timeout=120s

# Check all resources
echo "=== All Resources Status ==="
kubectl get all

# Check deployments
echo "=== Deployments ==="
kubectl get deployments

# Check services
echo "=== Services ==="
kubectl get services

# Check pods
echo "=== Pods ==="
kubectl get pods -o wide
Step 6: Test the Application
bash
# Test the frontend service
curl http://localhost:30009/

# Or using node IP
NODE_IP=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
echo "Access the Guestbook at: http://$NODE_IP:30009/"
curl http://$NODE_IP:30009/
Step 7: Final Verification
bash
# Comprehensive verification
echo "=== Final Verification ==="

echo "1. Redis Master:"
kubectl get deployment redis-master
kubectl get pods -l app=redis-master

echo "2. Redis Slave:"
kubectl get deployment redis-slave
kubectl get pods -l app=redis-slave

echo "3. Frontend:"
kubectl get deployment frontend
kubectl get pods -l app=frontend

echo "4. Services:"
kubectl get services

echo "5. Pod Details:"
kubectl describe pods

echo "6. Testing Frontend Access:"
curl -s http://localhost:30009/ | head -5 || echo "Testing access..."
Expected Results:
Back-end Tier:

✅ Redis Master: 1 replica, container master-redis-devops, port 6379

✅ Redis Master Service: Port 6379

✅ Redis Slave: 2 replicas, container slave-redis-devops, port 6379

✅ Redis Slave Service: Port 6379

Front-end Tier:

✅ Frontend: 3 replicas, container php-redis-devops, port 80

✅ Frontend Service: NodePort on 30009

Environment Variables:

✅ GET_HOSTS_FROM: dns set in both Redis slave and Frontend

Resources:

✅ CPU: 100m requested for all containers

✅ Memory: 100Mi requested for all containers

The Guestbook application should now be accessible via the App button or at http://<node-ip>:30009/!


