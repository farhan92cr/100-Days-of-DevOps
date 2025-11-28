Step 1: Connect to Application Server 3
bash
ssh banner@stapp03
Password: BigGr33n

Step 2: Verify the Container is Running
First, let's check if the ubuntu_latest container is running:

bash
sudo docker ps | grep ubuntu_latest
If the container is not running, check all containers (including stopped ones):

bash
sudo docker ps -a | grep ubuntu_latest
Step 3: Create the New Image from the Container
Use the docker commit command to create a new image from the container:

bash
sudo docker commit ubuntu_latest demo:datacenter
Step 4: Verify the New Image was Created
Check that the new image demo:datacenter has been successfully created:

bash
sudo docker images | grep demo
You should see output similar to:

text
demo    datacenter    xxxxxxxxxxxx    X seconds ago    Size
Step 5: Additional Verification (Optional)
You can verify the image details and check that it was created from the correct container:

bash
# Check all images
sudo docker images

# Inspect the new image details
sudo docker image inspect demo:datacenter

# Verify the container still exists and is running
sudo docker ps | grep ubuntu_latest
Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to stapp03
ssh banner@stapp03

# Verify the ubuntu_latest container exists and is running
sudo docker ps | grep ubuntu_latest

# If container is not running, start it first
sudo docker start ubuntu_latest

# Create new image from the container
sudo docker commit ubuntu_latest demo:datacenter

# Verify the new image was created
sudo docker images | grep demo

# Optional: Detailed verification
sudo docker image inspect demo:datacenter | grep -A 5 "Container"
If Container is Stopped:
If the container is stopped, you'll need to start it first or use the container ID:

bash
# If container is stopped, start it
sudo docker start ubuntu_latest

# Or commit using container ID (if you prefer)
sudo docker ps -a | grep ubuntu_latest
sudo docker commit <container_id> demo:datacenter
Expected Results:
A new Docker image named demo:datacenter should be created

The image should be based on the current state of the ubuntu_latest container

The original ubuntu_latest container should remain unchanged and running

The operation should complete without errors

This process creates a snapshot of the container's current state, including any changes made to the filesystem, which is useful for backing up development work or creating custom images from existing containers.