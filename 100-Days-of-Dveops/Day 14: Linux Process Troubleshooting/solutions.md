ssh tony@stapp01
# Password: Ir0nM@n

# Verify:
sudo systemctl status httpd
sudo ss -tlnp | grep 8089

if any other service is running on 8089 stop it or htthpd is not installed, install it
sudo systemctl stop <service name>

sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
sudo sed -i 's/^Listen.*/Listen 8089/' /etc/httpd/conf/httpd.conf
sudo systemctl restart httpd

# Verify again:
sudo systemctl status httpd
sudo ss -tlnp | grep 8089


Same other servers
Step 2: Connect to stapp02 (steve)
bash
ssh steve@stapp02
# Password: Am3ric@

# Once logged in, run:
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
sudo sed -i 's/^Listen.*/Listen 8089/' /etc/httpd/conf/httpd.conf
sudo systemctl restart httpd

# Verify:
sudo systemctl status httpd
sudo ss -tlnp | grep 8089

# Exit stapp02
exit

Step 3: Connect to stapp03 (banner)
ssh banner@stapp03
# Password: BigGr33n

# Once logged in, run:
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
sudo sed -i 's/^Listen.*/Listen 8089/' /etc/httpd/conf/httpd.conf
sudo systemctl restart httpd

# Verify:
sudo systemctl status httpd
sudo ss -tlnp | grep 8089

# Exit stapp03
exit

Step 4: Test from jump host

# Test connectivity to all servers
curl -s http://stapp01:8089 && echo "stapp01:8089 - OK" || echo "stapp01:8089 - FAILED"
curl -s http://stapp02:8089 && echo "stapp02:8089 - OK" || echo "stapp02:8089 - FAILED"
curl -s http://stapp03:8089 && echo "stapp03:8089 - OK" || echo "stapp03:8089 - FAILED"