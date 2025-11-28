# Day 01 - Complete Solutions with Verification

## Task 1 Solution: User with Non-Interactive Shell
```bash
# Command Executed:
ssh tony@stapp01 "sudo useradd -s /sbin/nologin john"

# Verification Commands:
ssh tony@stapp01 "grep john /etc/passwd"
# Expected: john:x:[UID]:[GID]:::/home/john:/sbin/nologin

ssh tony@stapp01 "getent passwd john | cut -d: -f7"
# Expected: /sbin/nologin

# Test non-interactive shell (should fail):
ssh tony@stapp01 "sudo su - john"
# Expected: This account is currently not available.

Task 2 Solution: User with Expiry Date
# Command Executed:
ssh steve@stapp02 "sudo useradd -e 2024-01-28 james"

# Verification Commands:
ssh steve@stapp02 "sudo chage -l james | grep 'Account expires'"
# Expected: Account expires					2024-01-28

ssh steve@stapp02 "sudo grep james /etc/shadow | cut -d: -f8"
# Expected: 19286 (days since epoch until 2024-01-28)

ssh steve@stapp02 "getent passwd james"
# Expected: james:x:[UID]:[GID]:::/home/james:/bin/bash

Task 3 Solution: Disable SSH Root Login
# Commands Executed:
for server in stapp01 stapp02 stapp03; do
    ssh $server "sudo sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config"
    ssh $server "sudo systemctl restart sshd"
done

# Verification Commands:
for server in stapp01 stapp02 stapp03; do
    echo "=== $server ==="
    ssh $server "sudo grep PermitRootLogin /etc/ssh/sshd_config"
    ssh $server "sudo systemctl status sshd | grep Active"
done
# Expected: PermitRootLogin no

# Test root SSH access (should fail):
ssh root@stapp01
# Expected: Permission denied (publickey,gssapi-keyex,gssapi-with-mic)

