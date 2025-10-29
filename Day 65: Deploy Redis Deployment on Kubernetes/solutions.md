Step 1: Create the ConfigMap
bash
# Create the Redis configuration ConfigMap
cat > redis-config.yaml << 'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-redis-config
data:
  redis-config: |
    maxmemory 2mb
EOF

kubectl apply -f redis-config.yaml
Step 2: Create the Redis Deployment
bash
# Create the Redis deployment with all specified requirements
cat > redis-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis-container
        image: redis:alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "1"
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
EOF

kubectl apply -f redis-deployment.yaml
Step 3: Verify the Deployment
bash
# Check deployment status
kubectl get deployment redis-deployment

# Check pod status
kubectl get pods -l app=redis

# Wait for pod to be ready
kubectl rollout status deployment/redis-deployment --timeout=60s
Step 4: Verify Configuration and Volumes
bash
# Check ConfigMap
kubectl get configmap my-redis-config
kubectl describe configmap my-redis-config

# Check pod details to verify volume mounts
kubectl describe pods -l app=redis

# Verify the configuration is mounted correctly
kubectl exec -it $(kubectl get pods -l app=redis -o name) -- cat /redis-master/redis-config

# Test Redis functionality
kubectl exec -it $(kubectl get pods -l app=redis -o name) -- redis-cli ping
Complete Solution in Single File:
bash
# Create all resources in one file
cat > redis-complete.yaml << 'EOF'
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-redis-config
data:
  redis-config: |
    maxmemory 2mb
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis-container
        image: redis:alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "1"
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
EOF

# Apply everything
kubectl apply -f redis-complete.yaml
Step 5: Final Verification
bash
# Comprehensive verification
echo "=== Final Verification ==="

echo "1. ConfigMap Status:"
kubectl get configmap my-redis-config

echo "2. Deployment Status:"
kubectl get deployment redis-deployment

echo "3. Pod Status:"
kubectl get pods -l app=redis -o wide

echo "4. Pod Details (Volumes):"
kubectl describe pods -l app=redis | grep -A 10 -B 5 Mounts

echo "5. Verify ConfigMap Mount:"
kubectl exec -it $(kubectl get pods -l app=redis -o name) -- ls -la /redis-master/

echo "6. Verify EmptyDir Mount:"
kubectl exec -it $(kubectl get pods -l app=redis -o name) -- ls -la /redis-master-data/

echo "7. Test Redis Connection:"
kubectl exec -it $(kubectl get pods -l app=redis -o name) -- redis-cli ping

echo "8. Verify Redis Configuration:"
kubectl exec -it $(kubectl get pods -l app=redis -o name) -- redis-cli config get maxmemory
Expected Results:
✅ ConfigMap: my-redis-config with maxmemory setting

✅ Deployment: redis-deployment with 1 replica

✅ Container: redis-container using redis:alpine image

✅ CPU Request: 1 CPU requested

✅ Port: 6379 exposed

✅ Volumes:

data (emptyDir) mounted at /redis-master-data

redis-config (configMap) mounted at /redis-master

✅ Status: Pod in Running state, Redis responding to ping

Key Configuration Details:
ConfigMap: Contains Redis configuration with 2MB memory limit

CPU Request: Ensures Redis gets at least 1 CPU core

Volume Mounts:

ConfigMap provides runtime configuration

EmptyDir provides persistent storage for Redis data

Port 6379: Standard Redis port for connections

The Redis deployment is now ready for testing as an in-memory caching solution!



