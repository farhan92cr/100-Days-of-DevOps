# Day 02 - Complete Solutions with Verification

## Task 1 Solution: Grant Executable Permissions
```bash
# Command Executed:
ssh banner@stapp03 "sudo chmod a+x /tmp/xfusioncorp.sh"

# Verification Commands:
ssh banner@stapp03 "ls -l /tmp/xfusioncorp.sh"
# Expected: -rwxr-xr-x 1 owner group size date time /tmp/xfusioncorp.sh

ssh banner@stapp03 "sudo -u nobody /tmp/xfusioncorp.sh"
# Should execute without permission errors

Task 2 Solution: Install and Configure SELinux
# Commands Executed:
ssh banner@stapp03 "sudo yum install selinux-policy selinux-policy-targeted libselinux-utils policycoreutils -y"
ssh banner@stapp03 "sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config"

# Verification Commands:
ssh banner@stapp03 "sudo grep SELINUX= /etc/selinux/config"
# Expected: SELINUX=disabled

ssh banner@stapp03 "rpm -q selinux-policy selinux-policy-targeted"
# Should show packages installed

Task 3 Solution: Cron Job Setup
# Commands Executed:
for server in stapp01 stapp02 stapp03; do
    ssh $server "sudo yum install cronie -y"
    ssh $server "sudo systemctl start crond"
    ssh $server "sudo systemctl enable crond"
    ssh $server "echo '*/5 * * * * echo hello > /tmp/cron_text' | sudo tee /var/spool/cron/root"
done

# Verification Commands:
for server in stapp01 stapp02 stapp03; do
    echo "=== $server ==="
    ssh $server "sudo crontab -l -u root"
    ssh $server "sudo systemctl status crond"
    ssh $server "ls -l /tmp/cron_text 2>/dev/null || echo 'File not created yet'"
done
# Expected: */5 * * * * echo hello > /tmp/cron_text

# Wait 5 minutes and verify:
ssh stapp01 "cat /tmp/cron_text"
# Expected: hello

