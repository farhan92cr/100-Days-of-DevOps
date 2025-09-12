# Day 10 - Complete Solutions

## Task 1 Solution: Load Balancer Setup
```bash
# Install and configure nginx on stlb01
ssh loki@stlb01 "sudo yum install nginx -y"
ssh loki@stlb01 "sudo tee /etc/nginx/conf.d/loadbalancer.conf << 'EOF'
upstream app_servers {
    server stapp01:8080;
    server stapp02:8080;
    server stapp03:8080;
}
server {
    listen 80;
    location / {
        proxy_pass http://app_servers;
    }
}
EOF"

Task 2 Solution: Health Checks:
# Add health checks to nginx config
ssh loki@stlb01 "sudo tee /etc/nginx/conf.d/health.conf << 'EOF'
server {
    listen 8081;
    location /health {
        return 200 \"healthy\\n\";
        add_header Content-Type text/plain;
    }
}
EOF"

Task 3 Solution: SSL Termination:
# Generate self-signed SSL cert
ssh loki@stlb01 "sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx.key -out /etc/ssl/certs/nginx.crt -subj \"/CN=stlb01\""

# SSL configuration
ssh loki@stlb01 "sudo tee /etc/nginx/conf.d/ssl.conf << 'EOF'
server {
    listen 443 ssl;
    ssl_certificate /etc/ssl/certs/nginx.crt;
    ssl_certificate_key /etc/ssl/private/nginx.key;
    location / {
        proxy_pass http://app_servers;
    }
}
EOF"

