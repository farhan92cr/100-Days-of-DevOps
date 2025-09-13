
# notes.md
```markdown
# Day 03 - Key Learnings & Important Concepts

## 🔐 SSH Key Authentication:

### SSH Key Concepts
**Q: How does password-less SSH work?**
**A:** Public key is placed in `~/.ssh/authorized_keys` on remote server, private key remains on local machine

**Q: What are the security benefits?**
**A:** More secure than passwords, resistant to brute-force attacks, can use passphrase for additional security

### SSH Configuration
```bash
# Key generation
ssh-keygen -t rsa -b 4096 -C "comment" -f ~/.ssh/keyname

# Copy key to remote host
ssh-copy-id -i ~/.ssh/keyname user@host

# Test connection
ssh -i ~/.ssh/keyname user@host

⚙️ Ansible Setup:
Ansible Architecture
Q: What is Ansible?
A: Agentless automation tool using SSH for configuration management and deployment

Q: Why use specific version 4.8.0?
A: Ensures compatibility with existing scripts and playbooks

Ansible Components
# Inventory file
vim /etc/ansible/hosts

# Test connectivity
ansible all -m ping

# Basic playbook structure
- name: Example playbook
  hosts: all
  tasks:
    - name: Install package
      yum:
        name: httpd
        state: present


🗄️ Database Recovery:
MariaDB Troubleshooting
Q: Common MariaDB startup issues?
A: Corrupted database files, permission issues, port conflicts, disk space

Q: How to safely recover database?
A: Stop service, backup data, reinitialize database, restore permissions, restart service

Database Maintenance
# Check database status
systemctl status mariadb

# View error logs
tail -f /var/log/mariadb/mariadb.log

# Check listening ports
netstat -tlnp | grep mysql

# Verify database connectivity
mysql -u root -e "STATUS"

💡 Important Commands Reference:
SSH Key Management
# Generate SSH key without passphrase
ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa

# Copy key with custom port
ssh-copy-id -i ~/.ssh/id_rsa -p 2222 user@host

# Test specific key
ssh -i ~/.ssh/custom_key user@host


Ansible Installation
# Install specific version
pip3 install ansible==4.8.0

# Create symlinks for global access
ln -sf /usr/local/bin/ansible /usr/bin/ansible

# Verify installation
ansible --version
ansible localhost -m ping

Database Recovery
# Safe database reinitialization
mysql_install_db --user=mysql --ldata=/var/lib/mysql

# Fix permissions
chown -R mysql:mysql /var/lib/mysql
chmod -R 755 /var/lib/mysql

# Secure installation
mysql_secure_installation
🚨 Common Issues & Solutions:
SSH Connection Issues
Issue: Permission denied (publickey)
Solution: Check ~/.ssh/authorized_keys permissions (600), verify key copied correctly

Ansible Version Conflicts
Issue: Different ansible versions causing errors
Solution: Use virtual environments or specify exact version in requirements

Database Startup Failures
Issue: MariaDB fails to start with permission errors
Solution: Verify mysql user ownership of /var/lib/mysql, check SELinux context

📚 Best Practices:
Use SSH keys with passphrases for critical systems

Maintain version consistency for automation tools

Always backup database before recovery operations

Test database connectivity from application servers

Document SSH key locations and purposes

