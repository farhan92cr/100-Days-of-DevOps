Step 1: Check the Existing Secret File
bash
# Check if the media.txt file exists and view its content
ls -la /opt/media.txt
cat /opt/media.txt
Step 2: Create the Generic Secret
bash
# Create a generic secret from the file
kubectl create secret generic media --from-file=/opt/media.txt
Step 3: Verify the Secret was Created
bash
# Check the secret
kubectl get secrets
kubectl describe secret media

# View secret details (the data will be base64 encoded)
kubectl get secret media -o yaml
Step 4: Create the Pod that Uses the Secret
bash
cat > secret-devops-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: secret-devops
spec:
  containers:
  - name: secret-container-devops
    image: ubuntu:latest
    command: ["/bin/bash", "-c", "sleep infinity"]
    volumeMounts:
    - name: secret-volume
      mountPath: /opt/games
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: media
EOF
Step 5: Apply the Pod Configuration
bash
kubectl apply -f secret-devops-pod.yaml
Step 6: Verify the Pod is Running
bash
# Check pod status
kubectl get pods secret-devops

# Wait for pod to be ready
kubectl wait --for=condition=ready pod secret-devops --timeout=60s
Step 7: Verify the Secret is Mounted Correctly
bash
# Check the mounted secret in the container
kubectl exec secret-devops -- ls -la /opt/games/

# View the secret content
kubectl exec secret-devops -- cat /opt/games/media.txt
Complete Solution Commands:
bash
# 1. Verify the source file
echo "=== Checking source file ==="
cat /opt/media.txt

# 2. Create the secret
echo "=== Creating secret ==="
kubectl create secret generic media --from-file=/opt/media.txt

# 3. Verify secret creation
echo "=== Verifying secret ==="
kubectl get secret media
kubectl describe secret media

# 4. Create the pod
echo "=== Creating pod ==="
cat > secret-devops-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: secret-devops
spec:
  containers:
  - name: secret-container-devops
    image: ubuntu:latest
    command: ["/bin/bash", "-c", "sleep infinity"]
    volumeMounts:
    - name: secret-volume
      mountPath: /opt/games
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: media
EOF

kubectl apply -f secret-devops-pod.yaml

# 5. Wait for pod to be ready
echo "=== Waiting for pod ==="
kubectl wait --for=condition=ready pod secret-devops --timeout=60s

# 6. Verify secret mounting
echo "=== Verifying secret mount ==="
kubectl exec secret-devops -- ls -la /opt/games/
kubectl exec secret-devops -- cat /opt/games/media.txt

# 7. Final verification
echo "=== Final status ==="
kubectl get pod secret-devops
Alternative: Using Environment Variables (Optional)
If you wanted to use the secret as environment variables instead of volume mount:

bash
cat > secret-devops-env.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: secret-devops
spec:
  containers:
  - name: secret-container-devops
    image: ubuntu:latest
    command: ["/bin/bash", "-c", "sleep infinity"]
    env:
    - name: MEDIA_LICENSE
      valueFrom:
        secretKeyRef:
          name: media
          key: media.txt
EOF
Expected Results:
✅ Secret: media created from /opt/media.txt

✅ Pod: secret-devops with container secret-container-devops in Running state

✅ Mount: Secret mounted at /opt/games/ inside the container

✅ Content: media.txt file accessible at /opt/games/media.txt with the license content

Key Points:
Secrets are stored encoded in Kubernetes

When mounted as volumes, they become readable files in the container

The readOnly: true ensures the secret cannot be modified

Using sleep infinity keeps the container running for verification

Run these commands and the secret will be securely stored in Kubernetes and accessible to your pod!


