Step 1: Connect to Application Server 2
bash
ssh steve@stapp02
Password: Am3ric@

Step 2: Pull the nginx:alpine-perl Docker Image
Pull the specified nginx image:

bash
sudo docker pull nginx:alpine-perl
Step 3: Verify the Image was Pulled
Check that the image was successfully downloaded:

bash
sudo docker images | grep nginx
You should see output similar to:

text
nginx    alpine-perl    xxxxxxxxxxxx    X seconds ago    Size
Step 4: Create and Run the Container
Create a container named "official" with port mapping and ensure it stays running:

bash
sudo docker run -d --name official -p 5004:80 nginx:alpine-perl
Step 5: Verify the Container is Running
Check that the container is created and running:

bash
sudo docker ps | grep official
You should see output similar to:

text
CONTAINER ID   IMAGE               COMMAND                  CREATED         STATUS         PORTS                                   NAMES
xxxxxxxxxxxx   nginx:alpine-perl   "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:5004->80/tcp, :::5004->80/tcp   official
Step 6: Additional Verification
Perform additional checks to ensure everything is working correctly:

bash
# Check container status in detail
sudo docker ps -a | grep official

# Check container logs
sudo docker logs official

# Verify port mapping
sudo docker port official

# Test the nginx service from the host
curl http://localhost:5004
Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to stapp02
ssh steve@stapp02

# Pull nginx:alpine-perl image
sudo docker pull nginx:alpine-perl

# Verify image
sudo docker images | grep nginx

# Create and run the container with port mapping
sudo docker run -d --name official -p 5004:80 nginx:alpine-perl

# Verify container is running
sudo docker ps | grep official

# Test the nginx service
curl -I http://localhost:5004
Alternative Verification from Host:
You can also test the nginx service from outside the container:

bash
# Check if nginx is responding on port 5004
curl http://localhost:5004

# Check the response headers
curl -I http://localhost:5004

# Check if the port is listening on the host
sudo netstat -tulpn | grep 5004
Expected Results:
The nginx:alpine-perl image should be successfully pulled

A container named official should be created using this image

The container should be in running state (STATUS should show "Up")

Host port 5004 should be mapped to container port 80

Nginx should be accessible via http://localhost:5004 from the host

The container should remain running (the -d flag ensures it runs in detached mode)

Troubleshooting:
If the container fails to start or you encounter issues:

bash
# Check what went wrong
sudo docker logs official

# If container exists but is stopped, start it
sudo docker start official

# If container needs to be recreated (if there were errors)
sudo docker stop official && sudo docker rm official
sudo docker run -d --name official -p 5004:80 nginx:alpine-perl
The nginx container is now successfully deployed on Application Server 2 with the required port mapping and is ready to serve web traffic on port 5004.

