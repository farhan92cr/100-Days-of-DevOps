Step 1: Connect to Application Server 2
ssh steve@stapp02
Password: Am3ric@

Step 2: Pull the busybox:musl Image
Pull the specified busybox image with the musl tag:

sudo docker pull busybox:musl


Step 3: Verify the Image was Pulled
Check that the image was successfully downloaded:


sudo docker images | grep busybox
You should see output similar to:

busybox    musl    xxxxxxxxxxxx    X days ago     1.23MB


Step 4: Re-tag the Image as busybox:media
Create a new tag for the image:

sudo docker tag busybox:musl busybox:media


Step 5: Verify the New Tag
Confirm that both tags exist for the busybox image:

sudo docker images | grep busybox
You should now see output similar to:

text
busybox    media    xxxxxxxxxxxx    X days ago     1.23MB
busybox    musl     xxxxxxxxxxxx    X days ago     1.23MB
Note: Both tags will show the same image ID, confirming they reference the same image.

Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to stapp02
ssh steve@stapp02

# Pull the busybox:musl image
sudo docker pull busybox:musl

# Verify the image was pulled
sudo docker images | grep busybox

# Re-tag the image as busybox:media
sudo docker tag busybox:musl busybox:media

# Verify both tags exist
sudo docker images | grep busybox
Alternative Verification:
You can also verify using more detailed commands:

bash
# List all busybox images with details
sudo docker images busybox

# Check image details for both tags
sudo docker image inspect busybox:musl
sudo docker image inspect busybox:media
Expected Results:
The busybox:musl image should be successfully pulled from Docker Hub

A new tag busybox:media should be created pointing to the same image

Both busybox:musl and busybox:media should show the same image ID

The operation should complete without errors

This task demonstrates the common Docker workflow of pulling an image and creating additional tags for different environments or purposes, which is useful for organizing images in development and testing workflows.