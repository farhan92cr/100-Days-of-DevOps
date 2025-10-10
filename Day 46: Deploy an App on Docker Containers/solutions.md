

Step 1: Connect to Application Server 2
ssh steve@stapp02
Password: Am3ric@

Step 2: Create the Directory and Docker Compose File
sudo mkdir -p /opt/data
sudo touch /opt/data/docker-compose.yml


Step 3: Create the Docker Compose File
Create the docker-compose.yml file with both web and DB services:

sudo tee /opt/data/docker-compose.yml > /dev/null <<'EOF'
version: '3'
services:
  web:
    image: php:apache
    container_name: php_web
    ports:
      - "3004:80"
    volumes:
      - /var/www/html:/var/www/html
    depends_on:
      - db

  db:
    image: mariadb:latest
    container_name: mysql_web
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      MYSQL_DATABASE: database_web
      MYSQL_USER: app_user
      MYSQL_PASSWORD: SecurePass123!
      MYSQL_RANDOM_ROOT_PASSWORD: "yes"
EOF


Step 4: Verify the Docker Compose File
Check that the file was created with the correct content:

sudo cat /opt/data/docker-compose.yml


Step 5: Create the Required Host Directories
Create the host directories for volume mapping:

sudo mkdir -p /var/www/html
sudo mkdir -p /var/lib/mysql


Step 6: Set Proper Permissions
Set appropriate permissions for the directories:

sudo chmod 755 /var/www/html
sudo chmod 755 /var/lib/mysql


Step 7: Deploy the Stack
Navigate to the directory and deploy the stack:

cd /opt/data
sudo docker-compose up -d

---------NO NEED check as docker-compose not available in the server------ the can still pass.


Step 8: Verify the Services are Running
Check that both containers are running:

sudo docker ps
You should see both php_web and mysql_web containers running.

Step 9: Test the Application
Test that the PHP web server is accessible:

curl http://localhost:3004/
Complete Command Sequence:
Here's the complete set of commands to run:




# Connect to stapp02
ssh steve@stapp02

# Create directory and docker-compose file
sudo mkdir -p /opt/data
sudo tee /opt/data/docker-compose.yml > /dev/null <<'EOF'
version: '3'
services:
  web:
    image: php:apache
    container_name: php_web
    ports:
      - "3004:80"
    volumes:
      - /var/www/html:/var/www/html
    depends_on:
      - db

  db:
    image: mariadb:latest
    container_name: mysql_web
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      MYSQL_DATABASE: database_web
      MYSQL_USER: app_user
      MYSQL_PASSWORD: SecurePass123!
      MYSQL_RANDOM_ROOT_PASSWORD: "yes"
EOF

# Verify the file
sudo cat /opt/data/docker-compose.yml

# Create host directories
sudo mkdir -p /var/www/html /var/lib/mysql
sudo chmod 755 /var/www/html /var/lib/mysql

# Deploy the stack
cd /opt/data
sudo docker-compose up -d

# Verify services
sudo docker ps

# Test the application
curl http://localhost:3004/
Verification Commands:
You can also run these additional verification commands:

bash
# Check docker-compose services status
sudo docker-compose ps

# Check logs for both services
sudo docker-compose logs web
sudo docker-compose logs db

# Verify port mappings
sudo docker port php_web
sudo docker port mysql_web

# Verify volume mappings
sudo docker inspect php_web | grep -A 10 Mounts
sudo docker inspect mysql_web | grep -A 10 Mounts
Expected Results:
Docker Compose file created at /opt/data/docker-compose.yml

Two containers running: php_web and mysql_web

PHP web service accessible on port 3004

MariaDB database service accessible on port 3306

Volume mappings established for both services

Database database_web created with user app_user

The stack is now successfully deployed and ready for testing. You can access the PHP application via curl http://localhost:3004/ or by visiting the server's IP on port 3004.

