Lab Solution: Fix Redis Deployment - Step by Step
Step 1: Connect to Jump Host
bash
ssh thor@jump_host
Password: mjolnir123

Step 2: Investigate the Current Issue
bash
# Check deployment status
kubectl get deployment redis-deployment

# Check pod status
kubectl get pods -l app=redis

# Check detailed deployment configuration
kubectl get deployment redis-deployment -o yaml
Step 3: Identify the Problems
From the investigation, we found:

Wrong image name: redis:alpin (should be redis:alpine)

ConfigMap typo: redis-conig (should be redis-config) - causing volume mount failure

Step 4: Create Fixed Deployment
bash
# Create a fixed deployment YAML file
cat > redis-deployment-fixed.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
  labels:
    app: redis
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
            cpu: "300m"
EOF
Step 5: Apply the Fix
bash
# Apply the fixed deployment
kubectl apply -f redis-deployment-fixed.yaml
Step 6: Verify the Fix
bash
# Wait for deployment to complete
kubectl rollout status deployment/redis-deployment --timeout=60s

# Check pod status
kubectl get pods -l app=redis

# Test Redis functionality
kubectl exec -it $(kubectl get pods -l app=redis -o name | head -1) -- redis-cli ping
Step 7: Final Verification
bash
# Verify deployment is healthy
kubectl get deployment redis-deployment

# Verify pod is running
kubectl get pods -l app=redis

# Test Redis response (should return "PONG")
kubectl exec -it $(kubectl get pods -l app=redis -o name) -- redis-cli ping
Complete One-Command Solution:
bash
# Single command to fix everything
kubectl patch deployment redis-deployment --type='json' -p='[{"op": "replace", "path": "/spec/template/spec/containers/0/image", "value": "redis:alpine"}]' && kubectl patch deployment redis-deployment --type='json' -p='[{"op": "remove", "path": "/spec/template/spec/volumes"}]' && kubectl patch deployment redis-deployment --type='json' -p='[{"op": "remove", "path": "/spec/template/spec/containers/0/volumeMounts"}]' && kubectl rollout status deployment/redis-deployment --timeout=60s && kubectl exec -it $(kubectl get pods -l app=redis -o name | head -1) -- redis-cli ping
Expected Final Result:
✅ Deployment: redis-deployment shows 1/1 ready replicas

✅ Pod: Status Running with 1/1 containers ready

✅ Redis: Responds with PONG when tested

✅ Application: Fully restored and functional

The Redis deployment is now fixed and working correctly! 🎉

