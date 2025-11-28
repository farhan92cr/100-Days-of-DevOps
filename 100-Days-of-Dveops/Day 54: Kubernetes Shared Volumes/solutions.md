Step 1: Create the Pod Definition
bash
cat > volume-share-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-datacenter
spec:
  containers:
  - name: volume-container-datacenter-1
    image: debian:latest
    command: ["/bin/sleep"]
    args: ["infinity"]
    volumeMounts:
    - name: volume-share
      mountPath: /tmp/news
  - name: volume-container-datacenter-2
    image: debian:latest
    command: ["/bin/sleep"]
    args: ["infinity"]
    volumeMounts:
    - name: volume-share
      mountPath: /tmp/apps
  volumes:
  - name: volume-share
    emptyDir: {}
EOF
Step 2: Create the Pod
bash
kubectl apply -f volume-share-pod.yaml
Step 3: Verify Pod is Running
bash
kubectl get pods
Step 4: Create Test File in First Container
bash
kubectl exec -it volume-share-datacenter -c volume-container-datacenter-1 -- /bin/bash -c "echo 'Shared volume test content' > /tmp/news/news.txt"
Step 5: Verify File in Both Containers
bash
# Check in first container
kubectl exec volume-share-datacenter -c volume-container-datacenter-1 -- ls -la /tmp/news/

# Check in second container
kubectl exec volume-share-datacenter -c volume-container-datacenter-2 -- ls -la /tmp/apps/

# Verify content in second container
kubectl exec volume-share-datacenter -c volume-container-datacenter-2 -- cat /tmp/apps/news.txt


What This Demonstrates:
Shared Volume: Both containers share the same emptyDir volume

Different Mount Paths: Same volume mounted at /tmp/news and /tmp/apps

Data Sharing: Files created in one container are accessible in the other

emptyDir: Temporary storage that exists as long as the pod exists

Expected Result:
The news.txt file created in /tmp/news/ of container-1 will be visible in /tmp/apps/ of container-2, proving the volume is successfully shared between containers.
