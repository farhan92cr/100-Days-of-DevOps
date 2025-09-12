
## 3. notes.md
```markdown
# Day 01 - Key Learnings & Important Concepts

## 🔐 Security Concepts Learned:

### 1. Non-Interactive Shells
**Q: Why use /sbin/nologin shell?**
**A:** `/sbin/nologin` prevents interactive login while allowing the user to own processes and files. Used for service accounts that don't need shell access.

**Q: Difference between /sbin/nologin and /bin/false?**
**A:** 
- `/sbin/nologin`: Shows "This account is currently not available" message
- `/bin/false`: Simply exits with false return code, no message

### 2. User Expiry Dates
**Q: Why set user expiry dates?**
**A:** For temporary users, contractors, or time-bound access. Automatically disables account after specified date.

**Q: How to check expiry date?**
**A:** Use `chage -l username` or check 8th field in `/etc/shadow`

### 3. SSH Security Hardening
**Q: Why disable root SSH login?**
**A:** Prevents brute-force attacks on root account. Forces attackers to guess both username and password.

**Q: What's the alternative to root login?**
**A:** Use regular user with sudo privileges, then `sudo su -` if needed.

## 💡 Important Commands Reference:

### User Management:
```bash
# Create user with specific shell
useradd -s /sbin/nologin username

# Create user with expiry date
useradd -e YYYY-MM-DD username

# Modify user expiry
chage -E YYYY-MM-DD username

# Check user expiry
chage -l username

# Disable root login
sed -i 's/^#*PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config

# Restart SSH service
systemctl restart sshd

# Verify configuration
grep PermitRootLogin /etc/ssh/sshd_config

🚨 Common Issues & Solutions:
Issue: Useradd command fails
Solution: Use sudo or ensure you have appropriate privileges

Issue: SSH config change doesn't take effect
Solution: Always restart SSH service after config changes

Issue: Cannot verify user expiry
Solution: Check /etc/shadow file (requires root privileges)

📚 Best Practices:
Always verify user creation immediately

Document expiry dates for temporary users

Test SSH config changes from another session

Use service accounts with nologin shell for applications