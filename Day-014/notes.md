# Day 14 - Apache Service Recovery & Port Conflicts

## 🔧 Port Conflict Resolution
**Issue:** Sendmail was already using port 8089, preventing Apache from starting

**Solution:**
- Identified the conflicting service: `ss -tlnp | grep 8089`
- Stopped sendmail: `systemctl stop sendmail`
- Configured Apache to use port 8089
- Started Apache service

## 🚨 Common Port Conflicts
Services that commonly use non-standard ports:
- **Sendmail**: Port 8089 (sometimes used for mail submission)
- **Tomcat**: Port 8080, 8089, 8443
- **Jenkins**: Port 8080
- **Custom applications**: Various ports

## 🔍 Port Investigation Tools
```bash
# Find what's using a specific port
sudo ss -tlnp | grep :8089
sudo netstat -tlnp | grep :8089
sudo lsof -i :8089

# Find all listening ports
sudo ss -tlnp
sudo netstat -tlnp

⚡ Quick Fix Steps for Port Conflicts
Identify the service: ss -tlnp | grep :PORT

Decide: Change port or stop service

If changing service port: edit config file

If stopping service: systemctl stop SERVICE

Restart your application

💡 Best Practices
Always check port availability before configuring services

Use systemctl list-sockets to see port assignments

Document port usage across your infrastructure

Consider using port ranges above 1024 for custom services
