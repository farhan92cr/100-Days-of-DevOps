Day 18 - Step-by-Step Solution:

Step 1: Install HTTPD and PHP on All App Servers
# On each app server (stapp01, stapp02, stapp03)
ssh tony@stapp01 "sudo yum install httpd php php-mysqlnd -y"
ssh steve@stapp02 "sudo yum install httpd php php-mysqlnd -y"  
ssh banner@stapp03 "sudo yum install httpd php php-mysqlnd -y"

Step 2: Configure Apache to Serve on Port 5002:
ssh tony@stapp01
sudo sed -i 's/^Listen.*/Listen 5002/' /etc/httpd/conf/httpd.conf
sudo systemctl start httpd
sudo systemctl enable httpd

ssh steve@stapp02
sudo sed -i 's/^Listen.*/Listen 5002/' /etc/httpd/conf/httpd.conf
sudo systemctl start httpd
sudo systemctl enable httpd

ssh banner@stapp03
sudo sed -i 's/^Listen.*/Listen 5002/' /etc/httpd/conf/httpd.conf
sudo systemctl start httpd
sudo systemctl enable httpd

Step 3: Install and Configure MariaDB on DB Server:
# Connect to DB server
ssh peter@stdb01

# Install MariaDB server
sudo yum install mariadb-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb

Step 4: Secure MariaDB Installation and Create Database/User:
# Secure MariaDB (set root password, remove test DB, etc.)
sudo mysql_secure_installation
# Follow prompts to set root password and secure installation

# Access MySQL as root
sudo mysql -u root -p

# Create database and user
CREATE DATABASE kodekloud_db7;
CREATE USER 'kodekloud_gem'@'%' IDENTIFIED BY 'BruCStnMT5';
GRANT ALL PRIVILEGES ON kodekloud_db7.* TO 'kodekloud_gem'@'%';
FLUSH PRIVILEGES;
EXIT;

Step 5: Create PHP Test File to Verify Database Connection:
For stapp01 (tony):

ssh tony@stapp01
sudo tee /var/www/html/test_db.php << 'EOF'
<?php
$servername = "stdb01";
$username = "kodekloud_gem";
$password = "BruCStnMT5";
$dbname = "kodekloud_db7";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 
echo "App is able to connect to the database using user kodekloud_gem";
$conn->close();
?>
EOF

For stapp02 (steve):

ssh steve@stapp02
sudo tee /var/www/html/test_db.php << 'EOF'
<?php
$servername = "stdb01";
$username = "kodekloud_gem";
$password = "BruCStnMT5";
$dbname = "kodekloud_db7";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 
echo "App is able to connect to the database using user kodekloud_gem";
$conn->close();
?>
EOF

For stapp03 (banner):


ssh banner@stapp03
sudo tee /var/www/html/test_db.php << 'EOF'
<?php
$servername = "stdb01";
$username = "kodekloud_gem";
$password = "BruCStnMT5";
$dbname = "kodekloud_db7";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
} 
echo "App is able to connect to the database using user kodekloud_gem";
$conn->close();
?>
EOF

After creating the files, test each server:
# Test stapp01
ssh tony@stapp01 "curl http://localhost:5002/test_db.php"

# Test stapp02  
ssh steve@stapp02 "curl http://localhost:5002/test_db.php"

# Test stapp03
ssh banner@stapp03 "curl http://localhost:5002/test_db.php"


***Step 6: Configure Load Balancer (LBR Server)***

# Connect to LBR server
ssh loki@stlb01

# Configure nginx to load balance to app servers on port 5002
sudo tee /etc/nginx/conf.d/wordpress.conf << 'EOF'
upstream app_servers {
    server stapp01:5002;
    server stapp02:5002;
    server stapp03:5002;
}

server {
    listen 80;
    location / {
        proxy_pass http://app_servers;
    }
}
EOF

sudo systemctl restart nginx

***Step 7: Verify Everything Works***
# Test database connection from app servers
for server in stapp01 stapp02 stapp03; do
    echo "=== Testing $server ==="
    ssh $server "curl http://localhost:5002/test_db.php"
    echo ""
done

# Test from LBR
curl http://stlb01/test_db.php
# Should show: "App is able to connect to the database using user kodekloud_gem"

Step 8: Final Verification:
# Check Apache is running on port 5002
for server in stapp01 stapp02 stapp03; do
    ssh $server "sudo ss -tlnp | grep 5002"
done

# Check MariaDB is running
ssh peter@stdb01 "sudo systemctl status mariadb"

# Check nginx is running
ssh loki@stlb01 "sudo systemctl status nginx"


