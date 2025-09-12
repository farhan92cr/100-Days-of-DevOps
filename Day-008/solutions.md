# Day 08 - Complete Solutions with Verification

## Task 1 Solution: Network Connectivity Troubleshooting
```bash
# Diagnose network issues on stapp01
ssh tony@stapp01 "
echo '=== Network Diagnostics ==='
ip addr show
echo '--- Routing Table ---'
ip route
echo '--- DNS Configuration ---'
cat /etc/resolv.conf
echo '--- Connectivity Test ---'
ping -c 3 8.8.8.8
ping -c 3 google.com
"

# Fix DNS configuration on stapp01
ssh tony@stapp01 "sudo tee /etc/resolv.conf << 'EOF'
nameserver 8.8.8.8
nameserver 1.1.1.1
search stratos.xfusioncorp.com
EOF"

# Test yum functionality
ssh tony@stapp01 "sudo yum update --assumeno"
# Should show package lists without connection errors

# Verification Commands:
ssh tony@stapp01 "ping -c 3 google.com"
# Should successfully resolve and ping

ssh tony@stapp01 "nslookup mirrorlist.centos.org"
# Should return DNS resolution

Task 2 Solution: DNS Configuration
# Configure DNS on all servers
SERVERS=("stapp01" "stapp02" "stapp03" "stdb01" "stlb01" "stbkp01")

for server in "${SERVERS[@]}"; do
    ssh $server "sudo tee /etc/resolv.conf << 'EOF'
nameserver 8.8.8.8
nameserver 1.1.1.1
search stratos.xfusioncorp.com
EOF"
    
    # Test DNS resolution
    ssh $server "nslookup google.com && echo '✅ DNS working on $server' || echo '❌ DNS failed on $server'"
done

# Verification Commands:
for server in "${SERVERS[@]}"; do
    echo "--- $server ---"
    ssh $server "cat /etc/resolv.conf; nslookup github.com | grep Address"
done
# All servers should show proper DNS configuration and resolution

Task 3 Solution: Network Testing Script:
# Create network diagnostic script on jump host
ssh thor@jump_host "sudo tee /scripts/network_test.sh << 'EOF'
#!/bin/bash

SERVERS=(\"stapp01\" \"stapp02\" \"stapp03\" \"stdb01\" \"stlb01\" \"stbkp01\")
LOG_FILE=\"/var/log/network_test.log\"

echo \"\$(date) - Starting network test\" >> \$LOG_FILE

for server in \"\${SERVERS[@]}\"; do
    echo \"=== Testing \$server ===\" | tee -a \$LOG_FILE
    
    # Test ICMP connectivity
    if ping -c 2 \$server >/dev/null 2>&1; then
        echo \"✅ Ping: SUCCESS\" | tee -a \$LOG_FILE
    else
        echo \"❌ Ping: FAILED\" | tee -a \$LOG_FILE
    fi
    
    # Test SSH connectivity
    if ssh -o ConnectTimeout=5 \$server \"hostname\" >/dev/null 2>&1; then
        echo \"✅ SSH: SUCCESS\" | tee -a \$LOG_FILE
    else
        echo \"❌ SSH: FAILED\" | tee -a \$LOG_FILE
    fi
    
    # Test DNS resolution
    if ssh \$server \"nslookup google.com >/dev/null 2>&1\"; then
        echo \"✅ DNS: SUCCESS\" | tee -a \$LOG_FILE
    else
        echo \"❌ DNS: FAILED\" | tee -a \$LOG_FILE
    fi
    
    echo \"\" | tee -a \$LOG_FILE
done

echo \"\$(date) - Network test completed\" >> \$LOG_FILE
echo \"Report saved: \$LOG_FILE\"
EOF"

ssh thor@jump_host "sudo chmod +x /scripts/network_test.sh"

# Add to hourly cron for continuous monitoring
ssh thor@jump_host "echo '0 * * * * root /scripts/network_test.sh' | sudo tee /etc/cron.d/network_test"

# Verification Commands:
ssh thor@jump_host "sudo /scripts/network_test.sh"
ssh thor@jump_host "sudo tail -20 /var/log/network_test.log"
# Should show network test results for all servers

