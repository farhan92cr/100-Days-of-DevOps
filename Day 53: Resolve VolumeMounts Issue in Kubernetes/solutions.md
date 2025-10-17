Issue Summary:
The nginx-phpfpm pod had incorrect ConfigMap configuration - it contained events and http blocks which are only valid in the main nginx.conf file, not in server config files.

Solution Steps:
Step 1: Fixed ConfigMap
bash
# Removed events/http blocks, kept only server block
kubectl apply -f nginx-config-fixed.yaml
Content:

yaml
data:
  default.conf: |
    server {
      listen 8099;
      root /var/www/html;
      # ... server configuration
    }
Step 2: Fixed Pod Mount
bash
kubectl delete pod nginx-phpfpm
kubectl apply -f nginx-phpfpm-final.yaml
Key fix: Mounted ConfigMap as default.conf in /etc/nginx/conf.d/ (not overriding main nginx.conf)

Step 3: Deploy Application
bash
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html/index.php
Root Cause:
ConfigMap had full nginx config (events + http blocks)

This was mounted as a server config file where only server blocks are allowed

Caused nginx syntax error and pod failure

Resolution:
Fixed: Used only server block in ConfigMap

Result: Nginx started successfully, PHP processing worked

Test: Application accessible on port 8099 ✅

The website is now working!



