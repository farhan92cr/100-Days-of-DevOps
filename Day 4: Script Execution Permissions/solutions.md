# Day 04 - Complete Solutions with Verification

## Task 1 Solution: Apache Port Configuration
```bash
# Commands Executed on stapp01:
ssh tony@stapp01
sudo sed -i 's/^Listen.*/Listen 6300/' /etc/httpd/conf/httpd.conf
sudo systemctl start httpd
sudo systemctl enable httpd

# Verification Commands:
sudo systemctl status httpd
# Expected: active (running)

sudo ss -tlnp | grep 6300
# Expected: LISTEN on port 6300

curl http://localhost:6300
# Should show Apache default page

# From jump host:
curl http://stapp01:6300
# Should be accessible

Task 2 Solution: Firewall Configuration with iptables
# Commands Executed on all app servers:
for server in stapp01 stapp02 stapp03; do
    ssh $server "sudo yum install iptables-services -y"
    ssh $server "sudo iptables -F"
    ssh $server "sudo iptables -P INPUT DROP"
    ssh $server "sudo iptables -P FORWARD DROP" 
    ssh $server "sudo iptables -P OUTPUT ACCEPT"
    ssh $server "sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT"
    ssh $server "sudo iptables -A INPUT -i lo -j ACCEPT"
    ssh $server "sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT"
    ssh $server "sudo iptables -A INPUT -p tcp --dport 6000 -s 172.16.238.14 -j ACCEPT"
    ssh $server "sudo iptables -A INPUT -p tcp --dport 6000 -j DROP"
    ssh $server "sudo iptables -A INPUT -p icmp -j ACCEPT"
    ssh $server "sudo service iptables save"
    ssh $server "sudo systemctl enable iptables"
    ssh $server "sudo systemctl start iptables"
done

# Verification Commands:
for server in stapp01 stapp02 stapp03; do
    echo "=== $server ==="
    ssh $server "sudo iptables -L -n -v | grep 6000"
done
# Expected: ACCEPT from 172.16.238.14, DROP from anywhere

# Test from LBR host (should work):
ssh loki@stlb01 "curl http://stapp01:6000"

# Test from jump host (should be blocked):
ssh thor@jump_host "curl http://stapp01:6000"
# Expected: Connection refused or timeout

Task 3 Solution: Tomcat Deployment
# Commands Executed on stapp03:
ssh banner@stapp03
sudo yum install tomcat tomcat-webapps tomcat-admin-webapps -y
sudo sed -i 's/port="8080"/port="8086"/' /etc/tomcat/server.xml
sudo rm -rf /var/lib/tomcat/webapps/ROOT
sudo cp /tmp/ROOT.war /var/lib/tomcat/webapps/
sudo chown tomcat:tomcat /var/lib/tomcat/webapps/ROOT.war
sudo systemctl start tomcat
sudo systemctl enable tomcat

# Verification Commands:
sudo systemctl status tomcat
# Expected: active (running)

sudo ss -tlnp | grep 8086
# Expected: LISTEN on port 8086

curl http://localhost:8086
# Should show deployed application

# From jump host:
curl http://stapp03:8086
# Should be accessible

