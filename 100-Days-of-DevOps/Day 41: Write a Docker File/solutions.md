Step 1: Connect to Application Server 2
ssh steve@stapp02
Password: Am3ric@

Step 2: Create the Directory and Dockerfile
First, create the directory and the Dockerfile:

sudo mkdir -p /opt/docker
sudo touch /opt/docker/Dockerfile


Step 3: Create the Dockerfile Content
Now, let's create the Dockerfile with the required configuration:

sudo tee /opt/docker/Dockerfile > /dev/null <<'EOF'
# Use ubuntu:24.04 as base image
FROM ubuntu:24.04

# Set environment variable to avoid interactive prompts during installation
ENV DEBIAN_FRONTEND=noninteractive

# Update package list and install apache2
RUN apt-get update && \
    apt-get install -y apache2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Configure Apache to listen on port 3003 instead of default port 80
RUN sed -i 's/Listen 80/Listen 3003/' /etc/apache2/ports.conf

# Expose port 3003
EXPOSE 3003

# Start Apache in foreground mode
CMD ["apache2ctl", "-D", "FOREGROUND"]
EOF


Step 4: Verify the Dockerfile Content
Check that the Dockerfile was created with the correct content:

sudo cat /opt/docker/Dockerfile
Step 5: Build the Docker Image
Now, let's build the image from the Dockerfile:

cd /opt/docker
sudo docker build -t custom-apache:latest .
Step 6: Verify the Image was Built
Check that the image was successfully built:

sudo docker images | grep custom-apache


Step 7: Test the Image (Optional)
You can test the image by running a container:

sudo docker run -d -p 3003:3003 --name test-apache custom-apache:latest
Verify the container is running and Apache is listening on port 3003:

sudo docker ps | grep test-apache
sudo docker exec test-apache netstat -tulpn | grep 3003
