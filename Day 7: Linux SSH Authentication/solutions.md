# Day 07 - Complete Solutions with Verification

## Task 1 Solution: Log Rotation Configuration
```bash
# Create logrotate configuration for application logs on each app server
for server in stapp01 stapp02 stapp03; do
    ssh $server "sudo tee /etc/logrotate.d/app_logs << 'EOF'
/var/log/tomcat/*.log /var/log/httpd/*log {
    daily
    missingok
    rotate 7
    compress
    delaycompress
    notifempty
    create 644 tomcat tomcat
    postrotate
        /bin/systemctl reload tomcat > /dev/null 2>/dev/null || true
        /bin/systemctl reload httpd > /dev/null 2>/dev/null || true
    endscript
}
EOF"

    # Test logrotate configuration
    ssh $server "sudo logrotate -d /etc/logrotate.d/app_logs"
done

# Verification Commands:
ssh stapp01 "sudo ls -la /etc/logrotate.d/app_logs"
# Should show configuration file exists

ssh stapp01 "sudo logrotate -vf /etc/logrotate.d/app_logs"
# Should rotate logs and show verbose output

Task 2 Solution: Centralized Log Monitoring
# Configure rsyslog on app servers to send logs to jump host
JUMP_HOST="jump_host"
for server in stapp01 stapp02 stapp03 stdb01; do
    ssh $server "sudo tee /etc/rsyslog.d/central.conf << EOF
*.* @${JUMP_HOST}:514
EOF"
    ssh $server "sudo systemctl restart rsyslog"
done

# Configure jump host to receive logs
ssh thor@jump_host "sudo tee /etc/rsyslog.d/receive.conf << 'EOF'
\$ModLoad imudp
\$UDPServerRun 514
\$template RemoteLogs,\"/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log\"
*.* ?RemoteLogs
EOF"

ssh thor@jump_host "sudo systemctl restart rsyslog"
ssh thor@jump_host "sudo mkdir -p /var/log/remote"

# Verification Commands:
ssh stapp01 "logger 'Test message from stapp01'"
ssh thor@jump_host "sudo tail -f /var/log/remote/stapp01/logger.log"
# Should show test message within seconds

Task 3 Solution: Log Analysis Script
# Create log analysis script on jump host
ssh thor@jump_host "sudo tee /scripts/log_analyzer.sh << 'EOF'
#!/bin/bash

LOG_DIR=\"/var/log/remote\"
REPORT_FILE=\"/var/log/log_report_$(date +%Y%m%d).txt\"

echo \"=== Log Analysis Report - $(date) ===\" > \$REPORT_FILE
echo \"\" >> \$REPORT_FILE

for server_dir in \$LOG_DIR/*; do
    server=\$(basename \$server_dir)
    echo \"--- \$server ---\" >> \$REPORT_FILE
    
    # Count errors and warnings
    errors=\$(find \$server_dir -name \"*.log\" -exec grep -i \"error\\|fail\\|exception\" {} \; | wc -l)
    warnings=\$(find \$server_dir -name \"*.log\" -exec grep -i \"warn\\|alert\" {} \; | wc -l)
    
    echo \"Errors: \$errors\" >> \$REPORT_FILE
    echo \"Warnings: \$warnings\" >> \$REPORT_FILE
    echo \"\" >> \$REPORT_FILE
done

# Send report (optional)
cat \$REPORT_FILE | mail -s \"Daily Log Report\" admin@example.com

echo \"Report generated: \$REPORT_FILE\"
EOF"

ssh thor@jump_host "sudo chmod +x /scripts/log_analyzer.sh"

# Add to daily cron
ssh thor@jump_host "echo '0 2 * * * root /scripts/log_analyzer.sh' | sudo tee /etc/cron.d/log_analyzer"

# Verification Commands:
ssh thor@jump_host "sudo /scripts/log_analyzer.sh"
ssh thor@jump_host "sudo cat /var/log/log_report_*.txt"
# Should show log analysis report


