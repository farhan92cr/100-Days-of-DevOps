Step 1: Connect to each App Server and install iptables
For App Server 1 (stapp01):

ssh tony@stapp01
sudo yum install iptables-services -y

For App Server 2 (stapp02):
ssh steve@stapp02
sudo yum install iptables-services -y

For App Server 3 (stapp03):
ssh banner@stapp03
sudo yum install iptables-services -y

Step 2: Configure iptables rules for port 8083 on each server
For each app server, run:
# Flush existing rules
sudo iptables -F

# Set default policies
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT

# Allow established connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow loopback interface
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow SSH connections (port 22)
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow port 8083 ONLY from LBR host (stlb01 - 172.16.238.14)
sudo iptables -A INPUT -p tcp --dport 8083 -s 172.16.238.14 -j ACCEPT

# Block port 8083 for all other hosts
sudo iptables -A INPUT -p tcp --dport 8083 -j DROP

# Allow ICMP (ping)
sudo iptables -A INPUT -p icmp -j ACCEPT

Step 3: Save iptables rules and enable the service
On each app server:

# Save current rules to make them persistent
sudo service iptables save

# Enable iptables service to start on boot
sudo systemctl enable iptables

# Start iptables service
sudo systemctl start iptables

# Check iptables status
sudo systemctl status iptables


step 4: Verify the iptables rules
On each app server:
# Check current iptables rules
sudo iptables -L -n -v

# Check specific port 8083 rule
sudo iptables -L -n -v | grep 8083

# Verify rules are saved
sudo cat /etc/sysconfig/iptables

Step 5: Test the configuration
From LBR host (should work):
ssh loki@stlb01
curl http://stapp01:8083
telnet stapp01 8083

From Jump host (should be blocked):
ssh thor@jump_host
curl http://stapp01:8083
telnet stapp01 8083

Step 6: Final verification on each app server
Check the complete rule set:
sudo iptables -L -n --line-numbers

The output should show something like:
Chain INPUT (policy DROP)
num  target     prot opt source               destination
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
2    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
3    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:22
4    ACCEPT     tcp  --  172.16.238.14        0.0.0.0/0            tcp dpt:8083
5    DROP       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8083
6    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0

