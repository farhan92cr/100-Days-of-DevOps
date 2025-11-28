
# notes.md
```markdown
# Day 06 - Key Learnings & Important Concepts

## 📊 Service Monitoring:

### Systemd Service Management
**Q: What are systemd service states?**
**A:** 
- active: Service is running
- inactive: Service is stopped
- failed: Service failed to start
- not-found: Service unit doesn't exist

**Q: How to monitor service health?**
**A:** Use `systemctl is-active`, `systemctl status`, and journalctl for logs

### Service Monitoring Techniques
```bash
# Check service status
systemctl is-active servicename

# View service logs
journalctl -u servicename -f

# Check service dependencies
systemctl list-dependencies servicename

⚙️ Process Automation:
Cron Job Management
Q: What are cron time specifications?
A:

*/5 * * * * - Every 5 minutes

0 * * * * - Every hour

0 0 * * * - Daily at midnight

0 0 * * 0 - Weekly on Sunday

Q: Where are cron jobs stored?
A:

/etc/crontab - System crontab

/etc/cron.d/ - Additional cron files

/var/spool/cron/ - User crontabs

Automation Best Practices
# Use full paths in cron jobs
*/5 * * * * /usr/bin/python3 /scripts/monitor.py

# Redirect output to log files
*/5 * * * * /scripts/monitor.sh >> /var/log/monitor.log 2>&1

# Use locking to prevent overlapping runs
flock -n /tmp/monitor.lock -c /scripts/monitor.sh

📈 Resource Monitoring:
Performance Metrics
Q: What are critical resource thresholds?
A:

CPU: >80% sustained usage

Memory: >85% usage

Disk: >90% usage

Disk I/O: High await times

Q: How to interpret resource metrics?
A:

CPU: user vs system time, load average

Memory: used vs cached vs buffers

Disk: usage percentage and inodes

Monitoring Tools
# Real-time monitoring
top
htop
iotop

# Historical data
sar
vmstat 1 10

# Disk usage
df -h
du -sh /path/

# Memory usage
free -h
cat /proc/meminfo
💡 Important Commands Reference:
Service Management
bash
# Check service status
systemctl is-active servicename
systemctl is-enabled servicename

# Restart failed service
systemctl reset-failed servicename
systemctl start servicename

# Monitor service logs
journalctl -u servicename --since "5 minutes ago"


Process Monitoring
# Find process by name
pgrep -l processname

# Monitor process resources
pidstat -p PID 1 5

# Kill process gracefully
pkill -TERM processname

# Force kill process
pkill -KILL processname


Resource Checks
# CPU usage from bash
cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}')

# Memory usage calculation
mem_usage=$(free | awk '/Mem/{printf("%.2f"), $3/$2*100}')

# Disk usage check
disk_usage=$(df / --output=pcent | tail -1 | tr -d '% ')

🚨 Common Issues & Solutions:
Service Failure
Issue: Service constantly crashing
Solution: Check logs with journalctl -u service, investigate dependencies

High Resource Usage
Issue: CPU or memory spikes
Solution: Use top to identify process, strace for system calls, consider optimizations

Cron Job Failures
Issue: Cron jobs not executing
Solution: Check syslog for cron errors, verify PATH in cron, test manually

📚 Best Practices:
Implement gradual restarts for frequently failing services

Set up alert escalation for critical services

Use resource limits with cgroups for important processes

Maintain service dependency documentation

Regularly test service failover procedures

Monitor not just service status but also service responsiveness

Keep historical performance data for capacity planning


