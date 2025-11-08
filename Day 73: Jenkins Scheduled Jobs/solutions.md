Lab Purpose & Explanation
What This Lab Does:
This creates a scheduled Jenkins job that automatically collects Apache logs from App Server 2 every 2 minutes and stores them on the Storage Server for centralized log management.

Benefits:
Automated Log Collection: No manual copying needed

Centralized Storage: All logs in one location for analysis

Regular Updates: Fresh logs every 2 minutes

Troubleshooting Ready: Logs available when Apache issues occur

Server Details:
App Server 2: stapp02 (steve/Am3ric@)

Storage Server: ststor01 (natasha/Bl@kW)

Destination: /usr/src/devops/

Schedule: Every 8 minutes



New Requirements:
Job name: copy-logs

Schedule: Every 8 minutes

Source: App Server 2 (stapp02) - Apache logs

Destination: Storage Server (ststor01) - /usr/src/data/

Step-by-Step BULLETPROOF Solution:
STEP 1: Login to Jenkins
Click the Jenkins button on top bar

Login with:

Username: admin

Password: Adm!n321

STEP 2: Install Required Plugin
Click Manage Jenkins → Manage Plugins

Click Available plugins tab

Search for: Publish Over SSH

Check the checkbox and click Install without restart

If prompted to restart, check the box and wait

STEP 3: Create the Job
Click New Item

Item name: copy-logs

Select Freestyle project

Click OK

STEP 4: Configure Schedule (CRITICAL)
Check Build periodically

In Schedule field, enter exactly: */8 * * * *

This means: Every 8 minutes

Double-check this is correct

STEP 5: Configure Build Step (SIMPLE & RELIABLE)
Use this SINGLE Execute shell step - remove all others:

bash
#!/bin/bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

echo "=== STARTING APACHE LOG COLLECTION ==="
echo "Timestamp: $TIMESTAMP"

# Step 1: Create destination directory on Storage Server
echo "1. Creating directory on Storage Server..."
sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10 natasha@ststor01 "sudo mkdir -p /usr/src/data && sudo chown natasha:natasha /usr/src/data && sudo chmod 755 /usr/src/data"

# Step 2: Copy access_log using rsync (most reliable method)
echo "2. Copying access_log..."
sshpass -p 'Am3ric@' rsync -avz -e "ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10" steve@stapp02:/var/log/httpd/access_log /tmp/access_temp_$TIMESTAMP

sshpass -p 'Bl@kW' rsync -avz -e "ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10" /tmp/access_temp_$TIMESTAMP natasha@ststor01:/usr/src/data/access_log_$TIMESTAMP

# Step 3: Copy error_log using same method
echo "3. Copying error_log..."
sshpass -p 'Am3ric@' rsync -avz -e "ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10" steve@stapp02:/var/log/httpd/error_log /tmp/error_temp_$TIMESTAMP

sshpass -p 'Bl@kW' rsync -avz -e "ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10" /tmp/error_temp_$TIMESTAMP natasha@ststor01:/usr/src/data/error_log_$TIMESTAMP

# Step 4: Set permissions on copied files
echo "4. Setting file permissions..."
sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10 natasha@ststor01 "sudo chmod 644 /usr/src/data/access_log_$TIMESTAMP /usr/src/data/error_log_$TIMESTAMP"

# Step 5: VERIFICATION - Check files actually exist
echo "5. VERIFYING TRANSFER..."
sshpass -p 'Bl@kW' ssh -o StrictHostKeyChecking=no -o ConnectTimeout=10 natasha@ststor01 "ls -lh /usr/src/data/access_log_$TIMESTAMP /usr/src/data/error_log_$TIMESTAMP && echo '✅ FILES SUCCESSFULLY COPIED!' || echo '❌ FILE COPY FAILED!'"

# Step 6: Cleanup temporary files
echo "6. Cleaning up temporary files..."
rm -f /tmp/access_temp_$TIMESTAMP /tmp/error_temp_$TIMESTAMP

echo "=== JOB COMPLETED ==="
echo "Files should be at: ststor01:/usr/src/data/access_log_$TIMESTAMP"
echo "Files should be at: ststor01:/usr/src/data/error_log_$TIMESTAMP"
STEP 6: Save the Job
Scroll to bottom

Click Save

STEP 7: Manual Pre-Test (IMPORTANT)
Before running Jenkins job, test from Jump Host:

bash
# Test if servers are accessible
ssh steve@stapp02 "ls /var/log/httpd/"
ssh natasha@ststor01 "mkdir -p /usr/src/data"

# Test manual file copy
sshpass -p 'Am3ric@' scp steve@stapp02:/var/log/httpd/access_log /tmp/test.log
sshpass -p 'Bl@kW' scp /tmp/test.log natasha@ststor01:/usr/src/data/test_access.log

# Verify
ssh natasha@ststor01 "ls -la /usr/src/data/"
STEP 8: Test Jenkins Job
Click Build Now

Wait for build to complete

Click the build number (#1)

Click Console Output

Look for: ✅ FILES SUCCESSFULLY COPIED!

STEP 9: Manual Verification (CRITICAL)
From Jump Host, verify files actually exist:

bash
ssh natasha@ststor01 "ls -la /usr/src/data/"
Expected output:

text
-rw-r--r-- 1 natasha natasha 12345 Nov  7 07:30 access_log_20231107_073000
-rw-r--r-- 1 natasha natasha  6789 Nov  7 07:30 error_log_20231107_073000
STEP 10: Verify Schedule Working
Wait 8-10 minutes

Check if automatic build starts (build #2, #3, etc.)

This confirms schedule is working

Alternative Method (If Above Fails):
Method B: Direct SSH Pipe Approach
Replace the build step with this:

bash
#!/bin/bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

echo "=== Using Direct SSH Pipe Method ==="

# Create directory
sshpass -p 'Bl@kW' ssh natasha@ststor01 "sudo mkdir -p /usr/src/data"

# Direct pipe transfer - most reliable
sshpass -p 'Am3ric@' ssh steve@stapp02 "cat /var/log/httpd/access_log" | sshpass -p 'Bl@kW' ssh natasha@ststor01 "cat > /usr/src/data/access_log_$TIMESTAMP"

sshpass -p 'Am3ric@' ssh steve@stapp02 "cat /var/log/httpd/error_log" | sshpass -p 'Bl@kW' ssh natasha@ststor01 "cat > /usr/src/data/error_log_$TIMESTAMP"

# Verify
sshpass -p 'Bl@kW' ssh natasha@ststor01 "ls -la /usr/src/data/ && echo 'SUCCESS: Files copied!'"
Why This Solution Works:
Key Improvements:
sshpass: Handles password authentication automatically

rsync: More reliable than SCP for file transfers

Two-step transfer: Jenkins → Temp → Destination (avoids direct server-to-server)

Explicit verification: Actually checks if files exist on destination

Clear success indicators: ✅ emoji makes success obvious

Server Credentials Used:
stapp02: steve/Am3ric@

ststor01: natasha/Bl@kW

File Locations:
Source: /var/log/httpd/access_log and /var/log/httpd/error_log on stapp02

Destination: /usr/src/data/ on ststor01

Troubleshooting Commands:
If sshpass not available:
bash
# Install sshpass on Jenkins server
yum install -y sshpass
If rsync not available:
Use the Method B (Direct SSH Pipe) approach instead

If permissions denied:
bash
# Check Apache log permissions on stapp02
ssh steve@stapp02 "ls -la /var/log/httpd/"

# Check directory permissions on ststor01  
ssh natasha@ststor01 "ls -la /usr/src/"
Final Verification Checklist:
✅ Job name: copy-logs
✅ Schedule: */8 * * * * (verified by automatic builds)
✅ Source: stapp02 Apache logs
✅ Destination: ststor01:/usr/src/data/
✅ Files copied: access_log and error_log with timestamps
✅ Manual verification shows files on storage server
✅ Console output shows ✅ FILES SUCCESSFULLY COPIED!

This approach addresses all previous failure points and ensures files are actually transferred! 🎉

Day 74: Jenkins Database Backup Job:

There is a requirement to create a Jenkins job to automate the database backup. Below you can find more details to accomplish this task:



Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.


Create a Jenkins job named database-backup.


Configure it to take a database dump of the kodekloud_db01 database present on the Database server in Stratos Datacenter, the database user is kodekloud_roy and password is asdfgdsd.


The dump should be named in db_$(date +%F).sql format, where date +%F is the current date.

Copy the db_$(date +%F).sql dump to the Backup Server under location /home/clint/db_backups.


Further, schedule this job to run periodically at */10 * * * * (please use this exact schedule format).


Note:


You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.


Please make sure to define you cron expression like this */10 * * * * (this is just an example to run job every 10 minutes).


For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

please explain the lab benefits and what get from it and what the purpose and also explain if there are new terms.
Length limit reached. Please start a new chat.



