Lab Overview:
This lab was about setting up a Persistent Volume (PV) and Persistent Volume Claim (PVC) in Kubernetes to provide persistent storage for an nginx web server, then exposing it via a NodePort Service.

Step-by-Step Solution:
Step 1: Create PersistentVolume (PV)
Purpose: Provides actual storage (5GB) on the host at /mnt/security

bash
cat > pv-datacenter.yaml << 'EOF'
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-datacenter
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  hostPath:
    path: /mnt/security
EOF
kubectl apply -f pv-datacenter.yaml
Step 2: Create PersistentVolumeClaim (PVC)
Purpose: Requests storage (1GB) from the available PV

bash
cat > pvc-datacenter.yaml << 'EOF'
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-datacenter
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  resources:
    requests:
      storage: 1Gi
EOF
kubectl apply -f pvc-datacenter.yaml
Step 3: Create Pod with Volume Mount
Purpose: Runs nginx with the PVC mounted at document root

bash
cat > pod-datacenter.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: pod-datacenter
  labels:
    app: nginx
spec:
  containers:
  - name: container-datacenter
    image: nginx:latest
    volumeMounts:
    - name: storage-volume
      mountPath: /usr/share/nginx/html
  volumes:
  - name: storage-volume
    persistentVolumeClaim:
      claimName: pvc-datacenter
EOF
kubectl apply -f pod-datacenter.yaml
Step 4: Create NodePort Service
Purpose: Exposes the web server on port 30008 for external access

bash
cat > web-datacenter-service.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: web-datacenter
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30008
EOF
kubectl apply -f web-datacenter-service.yaml
Step 5: Fix the 403 Forbidden Issue
Problem: Empty volume caused nginx to return 403
Solution: Create index.html in the mounted directory

bash
kubectl exec pod-datacenter -- sh -c 'echo "<h1>Welcome to Datacenter</h1>" > /usr/share/nginx/html/index.html'
Step 6: Verify Everything
bash
kubectl get pv,pvc,pod,service
curl http://localhost:30008/
Key Learning:
PV = Actual storage provision

PVC = Storage request from applications

Volume Mount = Makes storage available to containers

Service = Provides network access to pods

Empty volumes cause 403 errors - must add content

The application is now accessible at http://localhost:30008 ✅