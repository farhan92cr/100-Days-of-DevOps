# Day 11 - Complete Solutions

## Task 1 Solution: SSH Hardening
```bash
# SSH security configuration
for server in stapp01 stapp02 stapp03 stdb01 stlb01 stbkp01; do
    ssh $server "sudo tee -a /etc/ssh/sshd_config << 'EOF'
Protocol 2
PermitEmptyPasswords no
X11Forwarding no
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 2
EOF"
    ssh $server "sudo systemctl restart sshd"
done

Task 2 Solution: Fail2Ban Installation
# Install and configure Fail2Ban
for server in stapp01 stapp02 stapp03; do
    ssh $server "sudo yum install epel-release fail2ban -y"
    ssh $server "sudo tee /etc/fail2ban/jail.local << 'EOF'
[sshd]
enabled = true
maxretry = 3
bantime = 3600
EOF"
    ssh $server "sudo systemctl enable fail2ban --now"
done

Task 3 Solution: File Integrity Monitoring:
# Basic file integrity setup
ssh thor@jump_host "sudo tee /scripts/file_check.sh << 'EOF'
#!/bin/bash
FILES=(\"/etc/passwd\" \"/etc/shadow\" \"/etc/ssh/sshd_config\")
for file in \"\${FILES[@]}\"; do
    if [ -f \"\$file\" ]; then
        md5sum \$file >> /var/log/file_integrity.log
    fi
done
EOF"

