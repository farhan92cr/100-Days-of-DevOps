# Day 09 - Complete Solutions

## Task 1 Solution: Database Backup Automation
```bash
# Backup script on stdb01
ssh peter@stdb01 "sudo tee /scripts/db_backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR=\"/backup/database\"
DATE=\$(date +%Y%m%d_%H%M%S)
mkdir -p \$BACKUP_DIR
mysqldump -u root --all-databases > \$BACKUP_DIR/full_backup_\$DATE.sql
gzip \$BACKUP_DIR/full_backup_\$DATE.sql
find \$BACKUP_DIR -name \"*.sql.gz\" -mtime +7 -delete
EOF"

ssh peter@stdb01 "sudo chmod +x /scripts/db_backup.sh"
ssh peter@stdb01 "echo '0 2 * * * root /scripts/db_backup.sh' | sudo tee /etc/cron.d/db_backup"

Task 2 Solution: Backup Encryption:
# GPG encryption setup
ssh peter@stdb01 "sudo yum install gnupg2 -y"
ssh peter@stdb01 "gpg --gen-key --batch << EOF
Key-Type: RSA
Key-Length: 2048
Name-Real: DBAutoBackup
Expire-Date: 0
%no-protection
EOF"

# Encrypted backup script
ssh peter@stdb01 "sudo tee /scripts/encrypted_backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR=\"/backup/encrypted\"
mkdir -p \$BACKUP_DIR
mysqldump -u root --all-databases | gzip | gpg --encrypt --recipient DBAutoBackup > \$BACKUP_DIR/backup_\$(date +%Y%m%d).sql.gz.gpg
EOF"

Task 3 Solution: Backup Verification
# Verification script
ssh peter@stdb01 "sudo tee /scripts/verify_backup.sh << 'EOF'
#!/bin/bash
BACKUP=\"/backup/encrypted/backup_\$(date +%Y%m%d).sql.gz.gpg"
if [ -f \$BACKUP ]; then
    gpg --decrypt \$BACKUP | gunzip | head -n 10 | grep \"MySQL dump\"
    echo \"Backup verification successful\"
else
    echo \"Backup not found\"
fi
EOF"

