# Day 05 - Complete Solutions with Verification

## Task 1 Solution: Backup Script Creation
```bash
# Script created at /scripts/ecommerce_backup.sh on stapp01:
#!/bin/bash

SOURCE_DIR="/var/www/html/ecommerce"
BACKUP_DIR="/backup"
ZIP_FILE="xfusioncorp_ecommerce.zip"
BACKUP_SERVER="stbkp01"
BACKUP_USER="clint"
TEMP_BACKUP="$BACKUP_DIR/$ZIP_FILE"

mkdir -p $BACKUP_DIR
zip -r $TEMP_BACKUP $SOURCE_DIR

if [ $? -eq 0 ]; then
    echo "Backup archive created: $TEMP_BACKUP"
else
    echo "Error: Failed to create backup archive"
    exit 1
fi

scp $TEMP_BACKUP $BACKUP_USER@$BACKUP_SERVER:$BACKUP_DIR/

if [ $? -eq 0 ]; then
    echo "Backup copied to $BACKUP_SERVER:$BACKUP_DIR/"
else
    echo "Error: Failed to copy backup"
    exit 1
fi

echo "Backup process completed successfully!"

# Verification Commands:
ssh tony@stapp01 "ls -la /scripts/ecommerce_backup.sh"
# Expected: Script exists with proper permissions

ssh tony@stapp01 "chmod +x /scripts/ecommerce_backup.sh"
ssh tony@stapp01 "/scripts/ecommerce_backup.sh"
# Should execute without errors

Task 2 Solution: Secure Script Permissions
# Commands Executed on stapp01:
ssh tony@stapp01 "sudo mkdir -p /scripts /backup"
ssh tony@stapp01 "sudo chown tony:tony /scripts /backup"
ssh tony@stapp01 "chmod 755 /scripts/ecommerce_backup.sh"

# SSH Key Setup for Password-less Access:
ssh tony@stapp01 "ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N \"\""
ssh tony@stapp01 "ssh-copy-id clint@stbkp01"
# Enter password: H@wk3y3 when prompted

# Verification Commands:
ssh tony@stapp01 "ls -l /scripts/ecommerce_backup.sh"
# Expected: -rwxr-xr-x 1 tony tony

ssh tony@stapp01 "ssh clint@stbkp01 hostname"
# Should connect without password prompt

Task 3 Solution: Firewall Configuration
# Commands Executed on stapp01 for backup operations:
ssh tony@stapp01 "sudo iptables -A INPUT -p tcp --dport 22 -s stbkp01 -j ACCEPT"
ssh tony@stapp01 "sudo service iptables save"

# On stbkp01 for incoming backups:
ssh clint@stbkp01 "sudo iptables -A INPUT -p tcp --dport 22 -s stapp01 -j ACCEPT"
ssh clint@stbkp01 "sudo service iptables save"

# Verification Commands:
ssh tony@stapp01 "sudo iptables -L -n | grep stbkp01"
# Should show ACCEPT rule for backup server

ssh clint@stbkp01 "sudo iptables -L -n | grep stapp01"  
# Should show ACCEPT rule for app server

# Test backup connectivity:
ssh tony@stapp01 "scp /tmp/testfile clint@stbkp01:/tmp/"
# Should transfer without errors

