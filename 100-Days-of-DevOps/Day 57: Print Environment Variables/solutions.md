Complete Solution Commands:
bash
# 1. Create the pod YAML
cat > print-envars-pod.yaml << 'EOF'
apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting
spec:
  containers:
  - name: print-env-container
    image: bash:latest
    command: ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
    env:
    - name: GREETING
      value: "Welcome to"
    - name: COMPANY
      value: "DevOps"
    - name: GROUP
      value: "Industries"
  restartPolicy: Never
EOF

# 2. Create the pod
kubectl apply -f print-envars-pod.yaml

# 3. Wait for pod to complete (since restartPolicy: Never)
kubectl wait --for=condition=Ready pod/print-envars-greeting --timeout=30s

# 4. Check the output
echo "=== Pod Output ==="
kubectl logs print-envars-greeting

# 5. Verify pod status
echo "=== Pod Status ==="
kubectl get pod print-envars-greeting