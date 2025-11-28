Step 1: Connect to Application Server 1
bash
ssh tony@stapp01
Password: Ir0nM@n

Step 2: Pull the nginx:alpine Image
First, let's pull the nginx image with the alpine tag:

bash
sudo docker pull nginx:alpine
Step 3: Create and Run the nginx_1 Container
Now, create and run the container named nginx_1 using the nginx:alpine image:

bash
sudo docker run -d --name nginx_1 nginx:alpine
Step 4: Verify the Container is Running
Check if the container is created and running:

bash
sudo docker ps
You should see output similar to:

text
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS     NAMES
xxxxxxxxxxxx   nginx:alpine    "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   80/tcp    nginx_1
Step 5: Additional Verification (Optional)
You can also check the container status in more detail:

bash
# Check all containers (including stopped ones)
sudo docker ps -a

# Check container logs
sudo docker logs nginx_1

# Check container details
sudo docker inspect nginx_1
Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to stapp01
ssh tony@stapp01

# Pull nginx:alpine image
sudo docker pull nginx:alpine

# Create and run the container
sudo docker run -d --name nginx_1 nginx:alpine

# Verify container is running
sudo docker ps
Expected Results:
A Docker container named nginx_1 should be created

The container should be using the nginx:alpine image

The container should be in a running state (STATUS should show "Up")

The container should have port 80 exposed (though not mapped to host ports)

The nginx container is now successfully deployed and running on Application Server 1. The -d flag runs the container in detached mode (in the background), and --name nginx_1 assigns the specified name to the container.

