Step 1: Create the Namespace
bash
kubectl create namespace iron-namespace-xfusion
Step 2: Create Iron Gallery Deployment
bash
cat > iron-gallery-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
      - name: iron-gallery-container-xfusion
        image: kodekloud/irongallery:2.0
        resources:
          limits:
            memory: "100Mi"
            cpu: "50m"
        volumeMounts:
        - name: config
          mountPath: /usr/share/nginx/html/data
        - name: images
          mountPath: /usr/share/nginx/html/uploads
      volumes:
      - name: config
        emptyDir: {}
      - name: images
        emptyDir: {}
EOF
Step 3: Create Iron DB Deployment
bash
cat > iron-db-deployment.yaml << 'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
      - name: iron-db-container-xfusion
        image: kodekloud/irondb:2.0
        env:
        - name: MYSQL_DATABASE
          value: "database_web"
        - name: MYSQL_ROOT_PASSWORD
          value: "RootPass123!"
        - name: MYSQL_PASSWORD
          value: "UserPass123!"
        - name: MYSQL_USER
          value: "app_user"
        volumeMounts:
        - name: db
          mountPath: /var/lib/mysql
      volumes:
      - name: db
        emptyDir: {}
EOF
Step 4: Create Iron DB Service
bash
cat > iron-db-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  selector:
    db: mariadb
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
  type: ClusterIP
EOF
Step 5: Create Iron Gallery Service
bash
cat > iron-gallery-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  type: NodePort
  selector:
    run: iron-gallery
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 32678
EOF
Step 6: Apply All Configurations
bash
# Apply all configurations
kubectl apply -f iron-gallery-deployment.yaml
kubectl apply -f iron-db-deployment.yaml
kubectl apply -f iron-db-service.yaml
kubectl apply -f iron-gallery-service.yaml
Complete Single File Solution:
bash
# Create all resources in one file
cat > iron-gallery-complete.yaml << 'EOF'
apiVersion: v1
kind: Namespace
metadata:
  name: iron-namespace-xfusion
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-gallery-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
      - name: iron-gallery-container-xfusion
        image: kodekloud/irongallery:2.0
        resources:
          limits:
            memory: "100Mi"
            cpu: "50m"
        volumeMounts:
        - name: config
          mountPath: /usr/share/nginx/html/data
        - name: images
          mountPath: /usr/share/nginx/html/uploads
      volumes:
      - name: config
        emptyDir: {}
      - name: images
        emptyDir: {}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iron-db-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
      - name: iron-db-container-xfusion
        image: kodekloud/irondb:2.0
        env:
        - name: MYSQL_DATABASE
          value: "database_web"
        - name: MYSQL_ROOT_PASSWORD
          value: "RootPass123!"
        - name: MYSQL_PASSWORD
          value: "UserPass123!"
        - name: MYSQL_USER
          value: "app_user"
        volumeMounts:
        - name: db
          mountPath: /var/lib/mysql
      volumes:
      - name: db
        emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  selector:
    db: mariadb
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
  type: ClusterIP
---
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  type: NodePort
  selector:
    run: iron-gallery
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 32678
EOF

# Apply everything at once
kubectl apply -f iron-gallery-complete.yaml
Step 7: Verify Everything is Running
bash
# Check all resources in the namespace
kubectl get all -n iron-namespace-xfusion

# Check deployments
kubectl get deployments -n iron-namespace-xfusion

# Check pods
kubectl get pods -n iron-namespace-xfusion

# Check services
kubectl get services -n iron-namespace-xfusion

# Check pod status in detail
kubectl describe pods -n iron-namespace-xfusion
Step 8: Test the Application
bash
# Test the Iron Gallery service (from jump_host)
curl http://localhost:32678/

# Or using node IP
NODE_IP=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
curl http://$NODE_IP:32678/
Expected Results:
✅ Namespace: iron-namespace-xfusion created

✅ Gallery Deployment: iron-gallery-deployment-xfusion with 1 replica

✅ DB Deployment: iron-db-deployment-xfusion with 1 replica

✅ DB Service: iron-db-service-xfusion type ClusterIP on port 3306

✅ Gallery Service: iron-gallery-service-xfusion type NodePort on port 32678

✅ Access: Iron Gallery installation page accessible via port 32678

The application should now be deployed and accessible! The installation page should appear when you access the service.


