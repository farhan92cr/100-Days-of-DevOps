Step 1: Connect to App Server 2 (stapp02)

ssh steve@stapp02
# Password: Am3ric@
Step 2: Install Nginx

sudo yum install nginx -y
Step 3: Configure Nginx to Use Port 8093

# Edit nginx configuration
sudo vi /etc/nginx/nginx.conf

# Find the server section and change to:
server {
    listen       8093;
    server_name  localhost;
    root         /var/www/html;
    ...
}


Step 4 (Corrected): Install PHP 8.3 and PHP-FPM for RHEL 9
sudo yum install epel-release -y
# Install the correct REMI repository for RHEL 9
sudo yum install https://rpms.remirepo.net/enterprise/remi-release-9.rpm -y

# Enable PHP 8.3 repository
sudo yum install yum-utils -y
sudo yum-config-manager --enable remi-php83

# Install PHP 8.3 and PHP-FPM
sudo yum install php83 php83-php-fpm -y

Step 5: Configure PHP-FPM Socket

# Create directory for PHP-FPM socket
sudo mkdir -p /var/run/php-fpm/

# Configure PHP-FPM to use the specified socket
sudo vi /etc/opt/remi/php83/php-fpm.d/www.conf

# Change the listen directive to:
listen = /var/run/php-fpm/default.sock

# Also ensure these settings:
listen.owner = nginx
listen.group = nginx
listen.mode = 0660
user = nginx
group = nginx
Step 6: Configure Nginx to Work with PHP-FPM

# Edit nginx configuration to handle PHP files
sudo vi /etc/nginx/nginx.conf

# Add PHP handling in the server block:
location ~ \.php$ {
    try_files $uri =404;
    fastcgi_pass unix:/var/run/php-fpm/default.sock;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}
Step 7: Start and Enable Services

# Start PHP 8.3 FPM service
sudo systemctl start php83-php-fpm
sudo systemctl enable php83-php-fpm

# Start Nginx
sudo systemctl start nginx
sudo systemctl enable nginx
Step 8: Set Proper Permissions

# Set ownership for web directory
sudo chown -R nginx:nginx /var/www/html

# Set permissions for PHP-FPM socket directory
sudo chown -R nginx:nginx /var/run/php-fpm/
Step 9: Verify PHP 8.3 Installation

# Check PHP version (should show 8.3)
/opt/remi/php83/root/usr/bin/php -v

# Verify PHP-FPM service
sudo systemctl status php83-php-fpm
Step 10: Test Configuration

# Check Nginx configuration
sudo nginx -t

# Check listening port
sudo ss -tlnp | grep 8093

# Test locally first
curl http://localhost:8093/index.php
curl http://localhost:8093/info.php
Step 11: Test from Jump Host

# From jump host terminal:
ssh thor@jump_host
curl http://stapp02:8093/index.php
curl http://stapp02:8093/info.php
Verification Commands:

# Verify PHP 8.3 is installed
sudo rpm -qa | grep php83

# Verify services are running
sudo systemctl status php83-php-fpm
sudo systemctl status nginx

# Check socket file
sudo ls -la /var/run/php-fpm/default.sock
This setup now uses the specific PHP 8.3 packages (php83 and php83-php-fpm) from the REMI repository, which should satisfy the version requirement. The service name is php83-php-fpm instead of just php-fpm.