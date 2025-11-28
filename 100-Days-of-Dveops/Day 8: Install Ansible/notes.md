
# notes.md
```markdown
# Day 08 - Network Essentials

## 🌐 DNS Configuration
**/etc/resolv.conf:**
- `nameserver`: DNS server IP (8.8.8.8, 1.1.1.1)
- `search`: Domain suffix for hostname completion

**Testing:**
```bash
nslookup domain.com
dig @8.8.8.8 google.com
host google.com

📡 Network Diagnostics
Connectivity Tests:

bash
ping hostname          # ICMP connectivity
traceroute hostname    # Network path
mtr hostname           # Continuous traceroute


Port Testing:
telnet host port       # TCP connectivity
nc -zv host port       # Port scan
ss -tlnp               # Listening ports


🔧 Troubleshooting Steps
Check physical connectivity (ip link)

Verify IP configuration (ip addr)

Test gateway reachability (ping gateway)

Check DNS resolution (nslookup)

Verify routing (ip route)

⚡ Quick Commands
# Flush DNS cache
systemctl restart systemd-resolved

# Check network interfaces
ip addr show

# Test specific port
timeout 2 bash -c "</dev/tcp/host/port" && echo "Open" || echo "Closed"

🚨 Common Issues
DNS resolution fails: Check /etc/resolv.conf, firewall rules

Network unreachable: Verify routing table, gateway

Port closed: Check service status, firewall configuration



