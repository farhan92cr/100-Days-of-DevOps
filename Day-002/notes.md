
# 📝 Day 02 - notes.md
```markdown
# Day 02 - Key Learnings & Important Concepts

## 🔐 File Permissions Concepts:

### 1. Linux Permission System
**Q: What does chmod a+x do?**
**A:** `a+x` adds execute permission for all users (user, group, others)

**Q: Difference between numeric and symbolic permissions?**
**A:** 
- Symbolic: `u+x` (user execute), `go-w` (group/others remove write)
- Numeric: `755` (rwxr-xr-x), `644` (rw-r--r--)

### 2. Special Permissions
**Q: What are special permissions?**
**A:** 
- SUID (4000): Runs as file owner
- SGID (2000): Runs as file group  
- Sticky bit (1000): Prevents file deletion in shared directories

## 🔐 SELinux Concepts:

### 1. SELinux Modes
**Q: What are SELinux modes?**
**A:** 
- Enforcing: Security policy active
- Permissive: Policy active but only logs violations
- Disabled: SELinux completely off

**Q: Why disable SELinux temporarily?**
**A:** For troubleshooting applications blocked by SELinux policies

### 2. SELinux Commands
```bash
# Check status
getenforce
sestatus

# Change mode temporarily
setenforce 0  # Permissive
setenforce 1  # Enforcing

# Check SELinux denials
ausearch -m avc -ts recent

⏰ Cron System:
1. Cron Syntax
**Q: What does */5 * * * * mean?**
A: Runs every 5 minutes

Minute: */5 (every 5 minutes)

Hour: * (every hour)

Day: * (every day)

Month: * (every month)

Weekday: * (every weekday)

2. Cron Locations
Q: Where are cron jobs stored?
A:

/var/spool/cron/username - User crontabs

/etc/crontab - System crontab

/etc/cron.d/ - Additional cron files

/etc/cron.hourly/daily/weekly/monthly/ - Script directories

💡 Important Commands Reference:
File Permissions:

# Symbolic permissions
chmod u+x file.sh      # Add execute for user
chmod go-w file.txt    # Remove write for group/others
chmod a+x script.sh    # Add execute for all

# Numeric permissions
chmod 755 script.sh    # rwxr-xr-x
chmod 644 file.txt     # rw-r--r--

# Special permissions
chmod 4755 binary      # SUID set
chmod 2755 directory   # SGID set
SELinux Management:

# Install SELinux
yum install selinux-policy selinux-policy-targeted

# Disable permanently
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config

# Check status
sestatus
getenforce
Cron Management:

# Install and start cron
yum install cronie
systemctl start crond
systemctl enable crond

# Edit user crontab
crontab -e

# List cron jobs
crontab -l

# System cron location
vim /etc/crontab
🚨 Common Issues & Solutions:
Issue: Script not executable
Solution: Ensure chmod +x and check shebang line (#!/bin/bash)

Issue: SELinux blocking applications
Solution: Use setenforce 0 temporarily or adjust SELinux policies

Issue: Cron job not running
Solution: Check cron logs at /var/log/cron, ensure crond service running

📚 Best Practices:
Use specific permissions instead of 777

Test SELinux in permissive mode before disabling

Always test cron jobs manually first

Document cron job purposes in comments