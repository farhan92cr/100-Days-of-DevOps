Step 1: Connect to Application Server 1
bash
ssh tony@stapp01
Password: Ir0nM@n

Step 2: Create the Docker Compose Directory and File
First, create the directory structure and the docker-compose.yml file:

bash
sudo mkdir -p /opt/docker
sudo touch /opt/docker/docker-compose.yml
Step 3: Create the Docker Compose File
Create the docker-compose.yml file with the required configuration:

bash
sudo tee /opt/docker/docker-compose.yml > /dev/null <<'EOF'
version: '3'
services:
  web_server:
    image: httpd:latest
    container_name: httpd
    ports:
      - "3003:80"
    volumes:
      - /opt/sysops:/usr/local/apache2/htdocs
EOF


---------------------------------------------------------------------------------------------------------------------------------------------------------------------- DONE till here- the lab will be passed ----- no need to check further as there is no docker-compose available in the server-------------------------------------------------------------------------------------------------------------------------------------------------------------------




Step 4: Verify the Docker Compose File
Check that the file was created with the correct content:

bash
sudo cat /opt/docker/docker-compose.yml
Step 5: Deploy the Container using Docker Compose
Navigate to the directory and deploy the container:

bash
cd /opt/docker
sudo docker-compose up -d
Step 6: Verify the Container is Running
Check that the container was created and is running:

bash
sudo docker ps | grep httpd
You should see output similar to:

text
CONTAINER ID   IMAGE           COMMAND              CREATED         STATUS         PORTS                                   NAMES
xxxxxxxxxxxx   httpd:latest   "httpd-foreground"   5 seconds ago   Up 4 seconds   0.0.0.0:3003->80/tcp, :::3003->80/tcp   httpd
Step 7: Additional Verification
Perform additional checks to ensure everything is configured correctly:

bash
# Check container status in detail
sudo docker ps -a | grep httpd

# Check container logs
sudo docker logs httpd

# Verify port mapping
sudo docker port httpd

# Verify volume mapping
sudo docker inspect httpd | grep -A 10 Mounts

# Test the httpd service from the host
curl -I http://localhost:3003



