Step 1: Connect to Application Server 1
ssh tony@stapp01
Password: Ir0nM@n

Step 2: Verify the Container and File
First, let's check if the ubuntu_latest container is running and if the source file exists:

# Check if container is running
sudo docker ps | grep ubuntu_latest

# Verify the source file exists
sudo ls -l /tmp/nautilus.txt.gpg


Step 3: Copy the File to the Container
Use the docker cp command to copy the file from the host to the container:

sudo docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/


Step 4: Verify the File was Copied Correctly
Check if the file was successfully copied to the container and verify its integrity:

# Verify file exists in the container
sudo docker exec ubuntu_latest ls -l /tmp/nautilus.txt.gpg

# Check file size to ensure it wasn't modified
sudo ls -l /tmp/nautilus.txt.gpg
sudo docker exec ubuntu_latest ls -l /tmp/nautilus.txt.gpg

# Compare checksums to ensure file integrity
sudo shasum /tmp/nautilus.txt.gpg
sudo docker exec ubuntu_latest shasum /tmp/nautilus.txt.gpg
Complete Command Sequence:
Here's the complete set of commands to run:

# Connect to stapp01
ssh tony@stapp01

# Verify container is running
sudo docker ps | grep ubuntu_latest

# Verify source file exists
sudo ls -l /tmp/nautilus.txt.gpg

# Copy file to container
sudo docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/

# Verify successful copy and file integrity
sudo docker exec ubuntu_latest ls -l /tmp/nautilus.txt.gpg

# Compare checksums
echo "Host file checksum:"
sudo shasum /tmp/nautilus.txt.gpg
echo "Container file checksum:"
sudo docker exec ubuntu_latest shasum /tmp/nautilus.txt.gpg
Alternative Verification Methods:
If shasum is not available in the container, you can use other methods:

bash
# Using md5sum
sudo md5sum /tmp/nautilus.txt.gpg
sudo docker exec ubuntu_latest md5sum /tmp/nautilus.txt.gpg

# Or check file size and permissions
sudo ls -la /tmp/nautilus.txt.gpg
sudo docker exec ubuntu_latest ls -la /tmp/nautilus.txt.gpg
Expected Results:
The file /tmp/nautilus.txt.gpg should be successfully copied from the Docker host to the ubuntu_latest container at /tmp/

The file should maintain its original content (checksums should match)

The file should not be modified during the copy operation

The operation should complete without errors

The docker cp command is designed to copy files between the host and containers while preserving file content and metadata, ensuring the encrypted file remains intact during the transfer.

