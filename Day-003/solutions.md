# Day 03 - Complete Solutions with Verification

## Task 1 Solution: Password-less SSH Setup
```bash
# Commands Executed on jump host:
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02  
ssh-copy-id banner@stapp03

# Verification Commands:
ssh tony@stapp01 "hostname"
ssh steve@stapp02 "hostname"
ssh banner@stapp03 "hostname"
# Expected: Should connect without password prompts

ssh tony@stapp01 "sudo hostname"
# Should execute sudo commands without password

Task 2 Solution: Ansible Installation
# Commands Executed on jump host:
sudo yum install python3-pip -y
sudo pip3 install ansible==4.8.0
sudo ln -sf /usr/local/bin/ansible /usr/bin/ansible
sudo ln -sf /usr/local/bin/ansible-playbook /usr/bin/ansible-playbook

# Verification Commands:
ansible --version
# Expected: ansible [core 2.11.12] (version 4.8.0)

which ansible ansible-playbook
# Expected: /usr/bin/ansible, /usr/bin/ansible-playbook

sudo -u nobody ansible --version
# Should work for all users

Task 3 Solution: Database Service Recovery
# Commands Executed on stdb01:
ssh peter@stdb01
sudo systemctl status mariadb
sudo tail -50 /var/log/mariadb/mariadb.log
sudo pkill -9 mysql
sudo pkill -9 mysqld
sudo rm -rf /var/lib/mysql/*
sudo mysql_install_db --user=mysql --ldata=/var/lib/mysql --force
sudo chown -R mysql:mysql /var/lib/mysql
sudo systemctl start mariadb
sudo mysql_secure_installation

# Verification Commands:
sudo systemctl status mariadb
# Expected: active (running)

sudo mysql -u root -e "SHOW DATABASES;"
# Expected: List of databases

curl http://stdb01:3306
# Should show MySQL connection response