Step 1: Connect to App Server 2 (stapp02)

ssh steve@stapp02
# Password: Am3ric@
Step 2: Install Apache HTTPD
sudo yum install httpd -y


Step 3: Configure Apache to Listen on Port 3004
sudo sed -i 's/^Listen.*/Listen 3004/' /etc/httpd/conf/httpd.conf


Step 4: Copy Website Backups from Jump Host
# First SSH to jump host
ssh thor@jump_host
# Password: mjolnir123

# From jump host, copy the websites to stapp02
scp -r /home/thor/ecommerce /home/thor/apps steve@stapp02:/tmp/
# Enter steve's password: Am3ric@ when prompted

Step 5: Move Websites to Apache Document Root
# On stapp02, move the websites to proper location
sudo mv /tmp/ecommerce /tmp/apps /var/www/html/

# Set proper permissions
sudo chown -R apache:apache /var/www/html/{ecommerce,apps}
sudo chmod -R 755 /var/www/html/{ecommerce,apps}

Step 6: Start and Enable Apache
sudo systemctl start httpd
sudo systemctl enable httpd


Step 7: Verify Configuration
# Check Apache is listening on port 3004
sudo ss -tlnp | grep 3004

# Check website directories exist
ls -la /var/www/html


Step 8: Test the Websites
# Test ecommerce website
curl http://localhost:3004/ecommerce/

# Test apps website  
curl http://localhost:3004/apps/