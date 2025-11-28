Step 1: Connect to Application Server 1
bash
ssh tony@stapp01
Password: Ir0nM@n

Step 2: Create the Docker Network with Bridge Driver and Specific Subnet
Create the docker network named "official" with bridge driver, subnet, and IP range:

bash
sudo docker network create \
  --driver bridge \
  --subnet 192.168.0.0/24 \
  --ip-range 192.168.0.0/24 \
  official
Step 3: Verify the Network was Created
Check that the network was created with the correct configuration:

bash
sudo docker network ls | grep official
Step 4: Inspect the Network Details
Verify all the configuration details including driver, subnet, and IP range:

bash
sudo docker network inspect official
Step 5: Detailed Verification
Check specific configuration parameters:

bash
# Check driver type
sudo docker network inspect official --format '{{.Driver}}'

# Check subnet configuration
sudo docker network inspect official --format '{{range .IPAM.Config}}{{.Subnet}}{{end}}'

# Check IP range
sudo docker network inspect official --format '{{range .IPAM.Config}}{{.IPRange}}{{end}}'
Complete Command Sequence:
Here's the complete set of commands to run:

bash
# Connect to stapp01
ssh tony@stapp01

# Create the docker network with all specified parameters
sudo docker network create \
  --driver bridge \
  --subnet 192.168.0.0/24 \
  --ip-range 192.168.0.0/24 \
  official

# Verify network creation
sudo docker network ls | grep official

# Inspect network details
sudo docker network inspect official
Alternative Single-Line Command:
If you prefer a single-line command:

bash
sudo docker network create --driver bridge --subnet 192.168.0.0/24 --ip-range 192.168.0.0/24 official
Expected Results:
After running the commands, you should see:

Network List Output:

text
NETWORK ID     NAME      DRIVER    SCOPE
xxxxxxxxxxx    official  bridge    local
Network Inspect Output should show:

Driver: "bridge"

Subnet: "192.168.0.0/24"

IPRange: "192.168.0.0/24"

Name: "official"

Verification Commands:
You can also test the network by running a container:

bash
# Optional: Test the network with a container
sudo docker run -d --name test-container --network official nginx:alpine

# Check container network configuration
sudo docker inspect test-container --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'

# Clean up test container
sudo docker stop test-container && sudo docker rm test-container
Important Notes:
The --driver bridge specifies the bridge network driver (which is the default, but explicitly set as required)

The --subnet 192.168.0.0/24 defines the network subnet

The --ip-range 192.168.0.0/24 sets the IP allocation range within the subnet

The network name "official" is created as specified in the ticket

The docker network has now been successfully created on App Server 1 with all the required specifications and is ready for future use by applications.

