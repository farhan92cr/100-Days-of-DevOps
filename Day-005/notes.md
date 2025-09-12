
# notes.md
```markdown
# Day 05 - Key Learnings & Important Concepts

## 📦 Backup Automation:

### Backup Strategies
**Q: What are common backup types?**
**A:** 
- Full backup: Complete data copy
- Incremental: Only changed files since last backup
- Differential: Changed files since last full backup

**Q: Why use zip for backups?**
**A:** Compression reduces storage needs and transfer time, maintains file structure

### Backup Best Practices
```bash
# Include timestamp in backup filename
ZIP_FILE="ecommerce_backup_$(date +%Y%m%d_%H%M%S).zip"

# Add checksum verification
md5sum backupfile.zip > backupfile.zip.md5

# Log backup operations
echo "$(date): Backup started" >> /var/log/backup.log


🔐 SSH Key Security:
SSH Key Management
Q: What are SSH key best practices?
A:

Use strong key types (RSA 4096, Ed25519)

Protect private keys with passphrases

Use different keys for different services

Regularly rotate keys

Q: How to manage multiple SSH keys?
A: Use ~/.ssh/config file for host-specific keys

SSH Configuration
# ~/.ssh/config example:
Host backup-server
    HostName stbkp01
    User clint
    IdentityFile ~/.ssh/backup_key
    Port 22

# Usage: ssh backup-server

🛡️ Firewall for Backup Services:
Backup Port Considerations
Q: What ports are typically used for backups?
A:

SSH (22): Secure file transfer

RSYNC (873): Efficient synchronization

Custom ports: Application-specific backup services

Q: How to secure backup connections?
A: Restrict source IPs, use VPN tunnels, implement network segmentation

iptables for Backup Networks
# Allow backup server specific access
iptables -A INPUT -p tcp --dport 22 -s backup_server_ip -j ACCEPT

# Limit backup bandwidth to avoid network impact
tc qdisc add dev eth0 root tbf rate 1mbit burst 32kbit latency 400ms

💡 Important Commands Reference:
Backup Scripting
# Create timestamped backup
backup_file="backup_$(date +%Y%m%d_%H%M%S).tar.gz"
tar -czf $backup_file /path/to/data

# Verify backup integrity
tar -tzf backupfile.tar.gz > /dev/null && echo "Backup OK"

# Cleanup old backups (keep 30 days)
find /backup -name "*.tar.gz" -mtime +30 -delete

SSH Key Operations
# Generate ED25519 key (more secure)
ssh-keygen -t ed25519 -C "backup-key" -f ~/.ssh/backup_ed25519

# Copy key with specific port
ssh-copy-id -i ~/.ssh/key.pub -p 2222 user@host

# Test key-based authentication
ssh -i ~/.ssh/key -o PasswordAuthentication=no user@host

Firewall Management
# Allow specific IP range
iptables -A INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j ACCEPT

# Rate limit connections
iptables -A INPUT -p tcp --dport 22 -m limit --limit 5/min -j ACCEPT

# Log firewall events
iptables -A INPUT -p tcp --dport 22 -j LOG --log-prefix "SSH Attempt: "

🚨 Common Issues & Solutions:
Backup Script Failures
Issue: Script permissions prevent execution
Solution: Ensure chmod +x and test with bash -n script.sh for syntax

SSH Connection Issues
Issue: Password prompts despite key setup
Solution: Check ~/.ssh/authorized_keys permissions (600), verify key copying

Firewall Blocking Backups
Issue: Backup transfers time out
Solution: Verify iptables rules, check network connectivity with traceroute

📚 Best Practices:
Implement backup rotation to manage storage space

Test backup restoration regularly

Encrypt sensitive backup data

Monitor backup job completion and log results

Use dedicated backup user accounts with limited privileges

Document backup procedures and recovery steps



