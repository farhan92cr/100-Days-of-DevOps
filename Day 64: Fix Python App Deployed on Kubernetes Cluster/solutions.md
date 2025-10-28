Step 1: Fix the Service Port Configuration
bash
# Fix the service port - change from 8080 to 80
kubectl patch service python-service-xfusion --type='json' -p='[{"op": "replace", "path": "/spec/ports/0/port", "value": 80}]'
Step 2: Verify the Service Configuration
bash
# Check the updated service configuration
kubectl get service python-service-xfusion -o yaml | grep -A 5 ports

# Verify endpoints are still correct
kubectl get endpoints python-service-xfusion
Step 3: Test Again
bash
# Test the application again
curl http://localhost:32345/

# Test with detailed output
curl -v http://localhost:32345/
Step 4: Alternative Test Methods
If it still doesn't work, let's test different approaches:

bash
# Test using the node IP directly
NODE_IP=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
echo "Node IP: $NODE_IP"
curl http://$NODE_IP:32345/

# Test from inside the cluster
kubectl run test-curl --image=curlimages/curl --rm -it --restart=Never -- curl http://python-service-xfusion:80/

# Test direct pod access
POD_IP=$(kubectl get pods -l app=python_app -o jsonpath='{.items[0].status.podIP}')
echo "Pod IP: $POD_IP"
curl http://$POD_IP:5000/
Complete Fix Solution:
bash
# 1. Fix the service port
echo "=== Fixing Service Port ==="
kubectl patch service python-service-xfusion --type='json' -p='[{"op": "replace", "path": "/spec/ports/0/port", "value": 80}]'

# 2. Verify the fix
echo "=== Verifying Service ==="
kubectl get service python-service-xfusion -o wide
kubectl get endpoints python-service-xfusion

# 3. Test the application
echo "=== Testing Application ==="
curl -s http://localhost:32345/ | head -5

# 4. If still not working, test alternatives
echo "=== Alternative Tests ==="
NODE_IP=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
echo "Testing via Node IP $NODE_IP:"
curl -s http://$NODE_IP:32345/ | head -5

# 5. Final verification
echo "=== Final Status ==="
kubectl get all -l app=python_app
If Still Not Working - Complete Service Recreation:
bash
# Delete and recreate the service with correct configuration
kubectl delete service python-service-xfusion

cat > python-service-correct.yaml << 'EOF'
apiVersion: v1
kind: Service
metadata:
  name: python-service-xfusion
spec:
  type: NodePort
  selector:
    app: python_app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
      nodePort: 32345
EOF

kubectl apply -f python-service-correct.yaml

# Test again
curl http://localhost:32345/
Expected Service Configuration After Fix:
port: 80 (external service port)

targetPort: 5000 (Flask app port inside container)

nodePort: 32345 (external access port)

selector: app=python_app (matches pod labels)

The Flask app is running correctly, so the issue is definitely with the service port configuration. Run the fix and the application should be accessible!



