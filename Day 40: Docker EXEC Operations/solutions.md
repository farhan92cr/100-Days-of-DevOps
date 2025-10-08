Step 1: Connect to Application Server 1
ssh tony@stapp01
Password: Ir0nM@n

Step 2: Access the kkloud Container
First, let's access the container interactively:

sudo docker exec -it kkloud /bin/bash


Step 3: Update Package Repository and Install Apache2
Once inside the container, update the package list and install Apache2:


apt update && apt install -y apache2


Step 4: Configure Apache to Listen on Port 8085
We need to modify the Apache configuration to change the listening port:

# Backup the original ports.conf file
cp /etc/apache2/ports.conf /etc/apache2/ports.conf.backup

# Edit the ports.conf file to change the listening port
sed -i 's/Listen 80/Listen 8085/' /etc/apache2/ports.conf

# Also check if there's a virtual host file for port 80 and update it
find /etc/apache2/sites-enabled/ -name "*.conf" -exec grep -l "80" {} \;
If there are virtual host files, update them as well:

# Update default virtual host if it exists
sed -i 's/:80/:8085/g' /etc/apache2/sites-enabled/000-default.conf
sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:8085>/' /etc/apache2/sites-enabled/000-default.conf


Step 5: Start Apache Service
Start the Apache service and enable it to start automatically:

# Start Apache service
service apache2 start

# Enable Apache to start on boot (if supported in container)
update-rc.d apache2 defaults
Step 6: Verify Apache Configuration
Check if Apache is running and listening on port 8085:

# Check Apache status
service apache2 status

# Check if Apache is listening on port 8085
netstat -tulpn | grep 8085

# Test with curl (from inside the container)
curl http://localhost:8085
Step 7: Exit and Verify Container State
Exit the container and verify it's still running:

exit

# Verify container is running
sudo docker ps | grep kkloud
Complete Command Sequence:
Here's the complete set of commands to run:


# Connect to stapp01
ssh tony@stapp01

# Access the container
sudo docker exec -it kkloud /bin/bash

# Inside the container:
# Update and install Apache
apt update && apt install -y apache2

# Configure Apache to listen on port 8085
cp /etc/apache2/ports.conf /etc/apache2/ports.conf.backup
sed -i 's/Listen 80/Listen 8085/' /etc/apache2/ports.conf

# Update virtual host configuration if needed
sed -i 's/:80/:8085/g' /etc/apache2/sites-enabled/000-default.conf 2>/dev/null || true
sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:8085>/' /etc/apache2/sites-enabled/000-default.conf 2>/dev/null || true

# Start Apache service
service apache2 start

# Verify Apache is running on port 8085
service apache2 status
netstat -tulpn | grep 8085

# Exit container
exit

# Verify container is still running
sudo docker ps | grep kkloud
Alternative Approach (if container doesn't have text editors):
If the container doesn't have sed or text editors, you can copy the configuration files from outside:


# From the host, copy a custom ports.conf file
echo "Listen 8085" | sudo docker exec -i kkloud tee /etc/apache2/ports.conf
Verification from Host:
You can also verify from the host machine:


# Check if Apache process is running inside container
sudo docker exec kkloud ps aux | grep apache

# Test Apache response
sudo docker exec kkloud curl -I http://localhost:8085

# Check container logs for Apache
sudo docker logs kkloud | grep -i apache
Expected Results:
Apache2 should be successfully installed in the kkloud container

Apache should be configured to listen on port 8085

Apache service should be running and accessible on localhost:8085, 127.0.0.1:8085, and the container's IP

The kkloud container should remain in running state

No specific IP or hostname binding - Apache should listen on all interfaces

The configuration ensures Apache listens on all available interfaces within the container on port 8085, making it accessible from any IP address that can reach the container.
