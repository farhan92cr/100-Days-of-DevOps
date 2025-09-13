
# 📄 Day 14 - README.md

```markdown
# Day 14 - Apache Service Recovery

## 🎯 Task Completed
Resolved Apache service unavailability caused by port conflict with Sendmail on port 8089 across all app servers.

## 📊 Issue Resolution
- Identified Sendmail occupying port 8089 on stapp01
- Stopped Sendmail service to free the port
- Configured Apache to listen on port 8089
- Verified Apache running successfully on all servers

## 🛠️ Skills Demonstrated
- Port conflict troubleshooting
- Service management and configuration
- Network port investigation
- Multi-server verification

## ✅ Verification
Apache successfully running on port 8089 on:
- stapp01 ✅
- stapp02 ✅  
- stapp03 ✅
