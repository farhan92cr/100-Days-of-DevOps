
# notes.md
```markdown
# Day 07 - Log Management Essentials

## 📝 Log Rotation
**Purpose:** Prevent disk space exhaustion by rotating and compressing old logs
**Key Config:**
- `daily/weekly/monthly`: Rotation frequency
- `rotate 7`: Keep 7 rotated logs
- `compress`: Gzip rotated logs
- `create`: Recreate log file with permissions

## 📡 Centralized Logging
**Benefits:** Single view of all logs, easier analysis, better security monitoring
**Tools:** rsyslog (lightweight), syslog-ng, ELK stack (advanced)

## 🔍 Log Analysis
**Common Patterns:**
- Errors: "error", "fail", "exception"
- Warnings: "warn", "alert", "critical"
- Security: "invalid", "unauthorized", "attack"

**Commands:**
```bash
# Live log monitoring
tail -f /var/log/messages

# Pattern searching
grep -i "error" /var/log/*.log

# Count occurrences
grep -c "pattern" file.log

⚡ Quick Reference
# Force log rotation
logrotate -vf /etc/logrotate.d/config

# Test rsyslog config
rsyslogd -N1

# Monitor log growth
du -sh /var/log/


🚨 Common Issues
Log rotation fails: Check permissions on log files

Central logging broken: Verify firewall allows port 514

Disk full: Implement log retention policies

