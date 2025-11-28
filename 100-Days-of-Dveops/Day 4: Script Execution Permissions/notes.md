
# notes.md
```markdown
# Day 04 - Key Learnings & Important Concepts

## 🌐 Web Server Configuration:

### Apache Configuration
**Q: How does Apache port configuration work?**
**A:** The `Listen` directive in `/etc/httpd/conf/httpd.conf` specifies which ports Apache binds to

**Q: What are common Apache configuration files?**
**A:** 
- `/etc/httpd/conf/httpd.conf` - Main configuration
- `/etc/httpd/conf.d/` - Additional configuration files
- `/var/www/html/` - Default web root

### Apache Management
```bash
# Check configuration syntax
apachectl configtest

# Restart Apache gracefully
systemctl reload httpd

# View Apache logs
tail -f /var/log/httpd/error_log

🔥 iptables Firewall:
iptables Concepts
Q: What are iptables chains?
A:

INPUT: Incoming traffic to server

FORWARD: Traffic being routed through server

OUTPUT: Outgoing traffic from server

Q: How does iptables rule ordering work?
A: Rules are processed top-down; first matching rule applies

iptables Rule Management
# List rules with line numbers
iptables -L -n --line-numbers

# Insert rule at specific position
iptables -I INPUT 3 -p tcp --dport 80 -j ACCEPT

# Delete rule by number
iptables -D INPUT 3

# Save rules permanently
service iptables save

# List rules with line numbers
iptables -L -n --line-numbers

# Insert rule at specific position
iptables -I INPUT 3 -p tcp --dport 80 -j ACCEPT

# Delete rule by number
iptables -D INPUT 3

# Save rules permanently
service iptables save

🐈 Tomcat Deployment:
Tomcat Architecture
Q: What is Tomcat's directory structure?
A:

/var/lib/tomcat/webapps/ - Deployed applications

/etc/tomcat/ - Configuration files

/var/log/tomcat/ - Log files

Q: How does WAR deployment work?
A: Tomcat automatically extracts WAR files to directory with same name in webapps/

Tomcat Management
# View Tomcat logs
tail -f /var/log/tomcat/catalina.out

# Check webapp deployment status
ls -la /var/lib/tomcat/webapps/

# Manual deployment
cp application.war /var/lib/tomcat/webapps/

💡 Important Commands Reference:
Apache Configuration
# Change listening port
sed -i 's/^Listen.*/Listen 6300/' /etc/httpd/conf/httpd.conf

# Check listening ports
netstat -tlnp | grep httpd

# Test Apache configuration
apachectl -t

iptables Configuration
# Allow specific IP and port
iptables -A INPUT -p tcp --dport 80 -s 192.168.1.100 -j ACCEPT

# Block port for all
iptables -A INPUT -p tcp --dport 80 -j DROP

# Save iptables rules
service iptables save
systemctl enable iptables

Tomcat Management
# Change Tomcat port
sed -i 's/port="8080"/port="8086"/' /etc/tomcat/server.xml

# Deploy WAR file
cp app.war /var/lib/tomcat/webapps/

# Set proper ownership
chown tomcat:tomcat /var/lib/tomcat/webapps/*.war

🚨 Common Issues & Solutions:
Apache Not Starting
Issue: Port already in use or configuration error
Solution: Check ss -tlnp | grep 6300, verify httpd.conf syntax

iptables Rules Not Persisting
Issue: Rules lost after reboot
Solution: Ensure iptables-service installed and service iptables save executed

Tomcat Application Not Accessible
Issue: WAR file not deploying properly
Solution: Check Tomcat logs, verify file permissions, ensure tomcat user can read WAR file

📚 Best Practices:
Always test web server configuration before restarting

Use specific IP restrictions in iptables instead of open ports

Document firewall rules for audit purposes

Monitor Tomcat logs during application deployment

Use version control for server configuration files

