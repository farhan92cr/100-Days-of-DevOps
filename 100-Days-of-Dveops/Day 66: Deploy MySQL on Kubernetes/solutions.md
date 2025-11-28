Step 1: Create the Secrets
bash
# Create mysql-root-pass secret
kubectl create secret generic mysql-root-pass \
  --from-literal=password=YUIidhb667

# Create mysql-user-pass secret
kubectl create secret generic mysql-user-pass \
  --from-literal=username=kodekloud_joy \
  --from-literal=password=8FmzjvFU6S

# Create mysql-db-url secret
kubectl create secret generic mysql-db-url \
  --from-literal=database=kodekloud_db8
Step 2: Create PersistentVolume
bash
cat > mysql-pv.yaml << 'EOF'
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /mnt/mysql-data
EOF

kubectl apply -f mysql-pv.yaml
Step 3: Create PersistentVolumeClaim
bash
cat > mysql-pv-claim.yaml << 'EOF'
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi
  storageClassName: manual
EOF

kubectl apply -f mysql-pv-claim.yaml
Step 4: Create MySQL Deployment
bash
cat > mysql-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-root-pass
              key: password
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-db-url
              key: database
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
EOF

kubectl apply -f mysql-deployment.yaml
Step 5: Create NodePort Service
bash
cat > mysql-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: NodePort
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
      nodePort: 30007
EOF

kubectl apply -f mysql-service.yaml


Step 6: Verify Everything is Working
bash
# Wait for deployment to be ready
kubectl rollout status deployment/mysql-deployment --timeout=120s

# Check all resources
echo "=== All Resources Status ==="
kubectl get all
kubectl get pv,pvc
kubectl get secrets

# Check pod status
echo "=== Pod Status ==="
kubectl get pods -l app=mysql

# Check service
echo "=== Service Status ==="
kubectl get service mysql

# Check pod logs to ensure MySQL started properly
echo "=== MySQL Logs ==="
kubectl logs -l app=mysql --tail=10

# Verify MySQL is running and accessible
echo "=== Testing MySQL ==="
kubectl exec -it $(kubectl get pods -l app=mysql -o name) -- mysql -u kodekloud_joy -p8FmzjvFU6S -e "SHOW DATABASES;"
Step 7: Final Verification Commands
bash
# Comprehensive verification
echo "=== Final Verification ==="

echo "1. Secrets:"
kubectl get secrets

echo "2. Persistent Volume:"
kubectl get pv mysql-pv

echo "3. Persistent Volume Claim:"
kubectl get pvc mysql-pv-claim

echo "4. Deployment:"
kubectl get deployment mysql-deployment

echo "5. Pod:"
kubectl get pods -l app=mysql

echo "6. Service:"
kubectl get service mysql

echo "7. Environment Variables in Pod:"
kubectl exec -it $(kubectl get pods -l app=mysql -o name) -- env | grep MYSQL

echo "8. Volume Mount:"
kubectl describe pods -l app=mysql | grep -A 10 -B 5 Mounts
Expected Results:
✅ Secrets: 3 secrets created with specified key-value pairs

✅ PV: mysql-pv with 250Mi capacity

✅ PVC: mysql-pv-claim requesting 250Mi, bound to PV

✅ Deployment: mysql-deployment with MySQL container

✅ Environment Variables: All 4 env vars set from secrets

✅ Volume Mount: PV mounted at /var/lib/mysql

✅ Service: mysql NodePort service on port 30007

✅ Status: MySQL pod in Running state, database accessible

The MySQL deployment is now complete with all security best practices using Kubernetes secrets and persistent storage!


