Verification Commands:
bash
# Verify Apache status
sudo systemctl status httpd

# Verify port listening
sudo netstat -tlnp | grep 3004

# Test both websites
curl -s http://localhost:3004/ecommerce/ | head -5
curl -s http://localhost:3004/apps/ | head -5
The websites should now be accessible at:

Ecommerce: http://stapp02:3004/ecommerce/

Apps: http://stapp02:3004/apps/

Both static websites are now deployed and serving content on port 3004 through Apache on App Server 2! ✅