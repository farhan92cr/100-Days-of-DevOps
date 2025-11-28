the configuration looks correct, but the curl command isn't showing the "Welcome!" message. Let's troubleshoot this:

Step 1: Check if Nginx is properly listening
bash
sudo ss -tlnp | grep nginx
# Should show nginx listening on port 80
Step 2: Check if backend servers are accessible from LBR
bash
# Test connectivity to each app server from LBR
curl -v http://stapp01:8089/
curl -v http://stapp02:8089/ 
curl -v http://stapp03:8089/
Step 3: Check Nginx error logs
bash
sudo tail -f /var/log/nginx/error.log
Step 4: Test the load balancer with verbose output
bash
curl -v http://localhost/
Step 5: Check if there are firewall issues
bash
sudo firewall-cmd --list-all
# If firewall is active, allow port 80
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
Step 6: Verify backend servers are responding
bash
# From jump host, test each app server directly
ssh thor@jump_host "curl http://stapp01:8089/"
ssh thor@jump_host "curl http://stapp02:8089/"
ssh thor@jump_host "curl http://stapp03:8089/"
Step 7: Check Nginx access logs
bash
sudo tail -f /var/log/nginx/access.log
Step 8: Test with full response (not just grep)
bash
curl http://localhost/
curl -v http://localhost/ 2>&1 | grep -E "(Welcome|HTTP|> GET)"
The most likely issues are:

Firewall blocking port 8089 between LBR and app servers

Apache not running on port 8089 on app servers

Network connectivity issues between servers

Nginx proxy configuration issue

Run the connectivity tests first to identify where the breakdown is occurring.

Excellent troubleshooting! 🎯 You found the root cause!

✅ Issue Identified and Resolved:
Problem: Backend servers were listening on port 8086 instead of 8089

Solution: Corrected the port configuration in Nginx load balancer

🔧 Quick Fix:
Update the upstream configuration in /etc/nginx/nginx.conf:

nginx
upstream app_servers {
    server stapp01:8086;    # Changed from 8089 to 8086
    server stapp02:8086;    # Changed from 8089 to 8086  
    server stapp03:8086;    # Changed from 8089 to 8086
}
✅ Verification:
bash
# Test after fixing the port
sudo nginx -t
sudo systemctl restart nginx
curl http://localhost/
# Should now show "Welcome!" from the backend servers
Great detective work! 🕵️‍♂️ Finding that the backend servers were on port 8086 instead of 8089 was the key to solving the 502 Bad Gateway error. The load balancer is now properly configured to forward traffic to the correct ports on the app servers.

Well done! 🚀
