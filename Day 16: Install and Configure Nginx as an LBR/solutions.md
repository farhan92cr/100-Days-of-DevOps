Day 16 - Step-by-Step Solution:

Step 1: Connect to LBR Server (stlb01)
bash
ssh loki@stlb01
# Password: Mischi3f
Step 2: Install Nginx
bash
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
Step 3: Configure Load Balancer in nginx.conf
bash
# Backup original config
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.backup

# Edit the main nginx configuration
sudo vi /etc/nginx/nginx.conf
Step 4: Add Load Balancer Configuration
Add the following configuration inside the http {} block in /etc/nginx/nginx.conf:

nginx
http {
    # Existing http configuration remains here
    
    # Load balancer upstream configuration
    upstream app_servers {
        server stapp01:8089;    # App Server 1 with Apache on port 8089
        server stapp02:8089;    # App Server 2 with Apache on port 8089
        server stapp03:8089;    # App Server 3 with Apache on port 8089
    }

    # Load balancer server configuration
    server {
        listen 80;
        
        location / {
            proxy_pass http://app_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
Step 5: Verify Apache is Running on App Servers
bash
# Check Apache status on all app servers
for server in stapp01 stapp02 stapp03; do
    echo "=== $server ==="
    ssh $server "sudo systemctl status httpd && sudo ss -tlnp | grep 8089"
    echo ""
done
Step 6: Test Nginx Configuration and Restart
bash
# Test nginx configuration syntax
sudo nginx -t

# If configuration test passes, restart nginx
sudo systemctl restart nginx
sudo systemctl status nginx
Step 7: Verify Load Balancer is Working
bash
# Check nginx is listening on port 80
sudo ss -tlnp | grep nginx

# Test load balancer locally
curl http://localhost/

# Test from jump host
ssh thor@jump_host "curl http://stlb01/"
Step 8: Final Verification
bash
# Check load balancer status
sudo systemctl status nginx

# Verify nginx configuration
sudo nginx -T | grep -A 20 "upstream app_servers"

# Test multiple requests to see load balancing
for i in {1..6}; do
    curl -s http://localhost/ | grep "Welcome"
done
Expected Behavior:
Nginx should be running on stlb01 port 80

Requests should be distributed across stapp01, stapp02, stapp03

All app servers should respond with "Welcome!" (from Apache on port 8089)

The StaticApp button should now work through the load balancer

Note: The configuration uses Apache's existing port 8089 on all app servers as required, and the load balancer distributes traffic across all three backend servers.