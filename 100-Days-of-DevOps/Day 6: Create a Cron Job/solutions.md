# Day 06 - Complete Solutions with Verification

## Task 1 Solution: Service Status Monitoring
```bash
# Monitoring script created at /scripts/service_monitor.sh on jump host:
#!/bin/bash

SERVERS=("stapp01" "stapp02" "stapp03" "stdb01")
SERVICES=("httpd" "tomcat" "mariadb")

for server in "${SERVERS[@]}"; do
    echo "=== Checking $server ==="
    for service in "${SERVICES[@]}"; do
        status=$(ssh $server "systemctl is-active $service 2>/dev/null || echo 'not-found'")
        if [ "$status" = "active" ]; then
            echo "✅ $service: RUNNING"
        elif [ "$status" = "not-found" ]; then
            echo "⚪ $service: NOT INSTALLED"
        else
            echo "❌ $service: STOPPED"
            # Restart attempt
            ssh $server "sudo systemctl start $service 2>/dev/null && echo 'Restarted successfully' || echo 'Restart failed'"
        fi
    done
    echo ""
done

# Verification Commands:
chmod +x /scripts/service_monitor.sh
/scripts/service_monitor.sh
# Expected: Status report for all services on all servers

Task 2 Solution: Process Management Automation
# Auto-restart script created at /scripts/auto_restart.sh on each app server:
#!/bin/bash

SERVICES=("httpd" "tomcat")

for service in "${SERVICES[@]}"; do
    if systemctl is-active --quiet $service; then
        echo "$(date): $service is running" >> /var/log/service_monitor.log
    else
        echo "$(date): $service is down - attempting restart" >> /var/log/service_monitor.log
        systemctl start $service
        if [ $? -eq 0 ]; then
            echo "$(date): $service restarted successfully" >> /var/log/service_monitor.log
            # Send notification (optional)
            echo "Service $service restarted on $(hostname)" | mail -s "Service Restart Alert" admin@example.com
        else
            echo "$(date): $service restart failed" >> /var/log/service_monitor.log
        fi
    fi
done

# Add to cron for automatic monitoring (on each server):
echo "*/5 * * * * root /scripts/auto_restart.sh" | sudo tee /etc/cron.d/service_monitor

# Verification Commands:
ssh stapp01 "sudo crontab -l | grep auto_restart"
# Should show cron entry

ssh stapp01 "sudo tail -f /var/log/service_monitor.log"
# Should show monitoring activity


Task 3 Solution: Resource Usage Monitoring
# Resource monitor script created at /scripts/resource_monitor.sh:
#!/bin/bash

THRESHOLD_CPU=80
THRESHOLD_MEM=85
THRESHOLD_DISK=90

echo "=== Resource Monitoring - $(hostname) - $(date) ==="

# CPU Usage
cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
echo "CPU Usage: $cpu_usage%"
[ ${cpu_usage%.*} -gt $THRESHOLD_CPU ] && echo "⚠️  HIGH CPU USAGE" >> /var/log/resource_alerts.log

# Memory Usage
mem_usage=$(free | grep Mem | awk '{printf("%.0f", $3/$2 * 100)}')
echo "Memory Usage: $mem_usage%"
[ $mem_usage -gt $THRESHOLD_MEM ] && echo "⚠️  HIGH MEMORY USAGE" >> /var/log/resource_alerts.log

# Disk Usage
disk_usage=$(df / | awk 'NR==2 {print $5}' | cut -d'%' -f1)
echo "Disk Usage: $disk_usage%"
[ $disk_usage -gt $THRESHOLD_DISK ] && echo "⚠️  HIGH DISK USAGE" >> /var/log/resource_alerts.log

echo ""

# Deploy to all servers
for server in stapp01 stapp02 stapp03 stdb01; do
    scp /scripts/resource_monitor.sh $server:/scripts/
    ssh $server "chmod +x /scripts/resource_monitor.sh"
    ssh $server "echo '*/10 * * * * root /scripts/resource_monitor.sh' | sudo tee /etc/cron.d/resource_monitor"
done

# Verification Commands:
/scripts/resource_monitor.sh
# Should show current resource usage

ssh stapp01 "sudo tail -f /var/log/resource_alerts.log"
# Should show alerts if thresholds exceeded
