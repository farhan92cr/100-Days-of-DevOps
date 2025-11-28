Step 1: Connect to stapp01

ssh tony@stapp01
# Password: Ir0nM@n
Step 2: Install and Configure Nginx

sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
Step 3: Move SSL Certificate to Proper Location

sudo mkdir -p /etc/nginx/ssl/
sudo mv /tmp/nautilus.crt /etc/nginx/ssl/
sudo mv /tmp/nautilus.key /etc/nginx/ssl/
sudo chmod 600 /etc/nginx/ssl/nautilus.*
Step 4: Create Index.html File

sudo mkdir -p /usr/share/nginx/html
echo "Welcome!" | sudo tee /usr/share/nginx/html/index.html
Step 5: Configure Nginx with SSL

sudo tee /etc/nginx/conf.d/ssl.conf << 'EOF'
server {
    listen       443 ssl;
    server_name  localhost;

    ssl_certificate /etc/nginx/ssl/nautilus.crt;
    ssl_certificate_key /etc/nginx/ssl/nautilus.key;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
EOF
Step 6: Restart Nginx and Test

sudo systemctl restart nginx
sudo systemctl status nginx

# Test locally first
curl -Ik https://localhost/
Step 7: Test from Jump Host

# From jump host terminal:
ssh thor@jump_host
curl -Ik https://stapp01/
Step 8: Verification Commands

# Check nginx status
sudo systemctl status nginx

# Check SSL files
sudo ls -la /etc/nginx/ssl/

# Check index.html
cat /usr/share/nginx/html/index.html

# Check listening ports
sudo ss -tlnp | grep nginx
Expected Results:
Nginx should be running on port 443

SSL certificate should be properly configured

curl from jump host should show "Welcome!" message

HTTPS connection should work with self-signed certificate

Note: Since it's a self-signed certificate, you might need to use -k flag with curl to ignore certificate validation:


curl -Ik https://stapp01/