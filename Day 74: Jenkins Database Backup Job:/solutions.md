Complete Step-by-Step Solution
Step 1: Access Jenkins and Verify Infrastructure
First, let's verify our servers can communicate:

bash
# From jump server, test connectivity
ssh thor@jump_host

# Test database server access
ssh peter@stdb01 "mysql -u kodekloud_roy -p'asdfgdsd' -e 'SHOW DATABASES;'"

# Test backup server access  
ssh clint@stbkp01 "ls -la /home/clint/"
Step 2: Create Jenkins Job with Proper Configuration
A. Jenkins Job Setup
Login to Jenkins: http://172.16.238.19:8080 (admin/Adm!n321)

Create New Item → Freestyle project → Name: database-backup

B. Source Code Management
Leave as None (we're not pulling code from repository)

C. Build Triggers
Build periodically: */10 * * * *

D. Build Environment
Check Delete workspace before build starts (optional, for clean runs)

E. Build Steps - Add Execute Shell
This is the CRITICAL part that was missing:

bash
#!/bin/bash

# Set variables
DB_SERVER="stdb01"
DB_USER="peter"
DB_PASS="Sp!dy"
DB_NAME="kodekloud_db01"
DB_USERNAME="kodekloud_roy"
DB_PASSWORD="asdfgdsd"
BACKUP_SERVER="stbkp01"
BACKUP_USER="clint"
BACKUP_PASS="H@wk3y3"
BACKUP_PATH="/home/clint/db_backups"
DATE_FORMAT=$(date +%F)
BACKUP_FILE="db_${DATE_FORMAT}.sql"

echo "Starting database backup process..."
echo "Backup file: ${BACKUP_FILE}"

# Method 1: Direct SSH execution (Recommended)
echo "Step 1: Creating database dump on ${DB_SERVER}"
sshpass -p "${DB_PASS}" ssh -o StrictHostKeyChecking=no ${DB_USER}@${DB_SERVER} \
  "mysqldump -u ${DB_USERNAME} -p'${DB_PASSWORD}' ${DB_NAME} > /tmp/${BACKUP_FILE}"

echo "Step 2: Copying backup to backup server"
sshpass -p "${DB_PASS}" ssh -o StrictHostKeyChecking=no ${DB_USER}@${DB_SERVER} \
  "sshpass -p '${BACKUP_PASS}' scp -o StrictHostKeyChecking=no /tmp/${BACKUP_FILE} ${BACKUP_USER}@${BACKUP_SERVER}:${BACKUP_PATH}/"

echo "Step 3: Cleaning up temporary files"
sshpass -p "${DB_PASS}" ssh -o StrictHostKeyChecking=no ${DB_USER}@${DB_SERVER} \
  "rm -f /tmp/${BACKUP_FILE}"

echo "Backup completed successfully!"
F. Alternative Method if sshpass not available:
bash
#!/bin/bash

# Alternative approach using SSH keys (if configured)
DATE_FORMAT=$(date +%F)
BACKUP_FILE="db_${DATE_FORMAT}.sql"

# Create backup on database server and copy directly
ssh peter@stdb01 << EOF
  # Create database dump
  mysqldump -u kodekloud_roy -p'asdfgdsd' kodekloud_db01 > /tmp/${BACKUP_FILE}
  
  # Copy to backup server
  scp /tmp/${BACKUP_FILE} clint@stbkp01:/home/clint/db_backups/
  
  # Cleanup
  rm -f /tmp/${BACKUP_FILE}
EOF

echo "Backup process completed"
Step 3: Pre-requisite Setup (Important!)
Before running the job, ensure these prerequisites are met:

A. Install Required Tools on Jenkins Server:
bash
# Access Jenkins server
ssh jenkins@172.16.238.19

# Install sshpass for password authentication
sudo yum install -y sshpass

# Or configure SSH key authentication between servers
B. Verify MySQL Access:
bash
# Test database connection and dump
ssh peter@stdb01 "mysqldump -u kodekloud_roy -p'asdfgdsd' kodekloud_db01 --no-data"
C. Create Backup Directory:
bash
# Ensure backup directory exists on backup server
ssh clint@stbkp01 "mkdir -p /home/clint/db_backups"
Step 4: Test the Job
Save the Jenkins job configuration

Click "Build Now" to test manually

Check Console Output for any errors

Verify on Backup Server:

bash
ssh clint@stbkp01 "ls -la /home/clint/db_backups/"
Step 5: Troubleshooting Common Issues
If you get "Permission denied" errors:
bash
# Configure SSH key authentication
ssh-keygen -t rsa
ssh-copy-id peter@stdb01
ssh-copy-id clint@stbkp01
If mysqldump fails:
bash
# Test database credentials
ssh peter@stdb01 "mysql -u kodekloud_roy -p'asdfgdsd' -e 'USE kodekloud_db01; SHOW TABLES;'"
If file copy fails:
bash
# Test SCP manually
ssh peter@stdb01 "echo 'test' > /tmp/test.txt && scp /tmp/test.txt clint@stbkp01:/home/clint/db_backups/"
Step 6: Verification Commands
After job runs successfully:

bash
# Check backup file on backup server
ssh clint@stbkp01 "ls -l /home/clint/db_backups/db_*.sql"

# Verify file content (should be SQL format)
ssh clint@stbkp01 "head -n 5 /home/clint/db_backups/db_$(date +%F).sql"
Key Differences from Previous Attempt:
Actual mysqldump command - Creates the SQL backup file

Proper SSH authentication - Uses sshpass or SSH keys

File transfer implementation - Actually copies the file between servers

Error handling - Includes echo statements for debugging

Cleanup process - Removes temporary files

This comprehensive approach should resolve the issues from your previous attempt and successfully create the automated database backup pipeline.




