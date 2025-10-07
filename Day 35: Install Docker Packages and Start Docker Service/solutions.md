Step 1: Connect to App Server 1
First, connect to stapp01 from the jump server:

ssh tony@stapp01
Password: Ir0nM@n

Step 2: Install Docker CE
Update the package repository and install Docker CE:

sudo yum update -y
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io


Step 3: Install Docker Compose
Install Docker Compose:

sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose


Step 4: Start and Enable Docker Service
Start the Docker service and enable it to start on boot:

sudo systemctl start docker
sudo systemctl enable docker


Step 5: Verify Installation
Check if Docker and Docker Compose are installed correctly:

# Check Docker version
docker --version

# Check Docker Compose version
docker-compose --version

# Check Docker service status
sudo systemctl status docker


Step 6: Test Docker (Optional)
Run a test container to verify everything is working:

sudo docker run hello-world
Expected Output:
Docker CE should be installed and running

Docker Compose should be installed and executable

The Docker service should be active and enabled

You should be able to run containers

Complete Command Sequence:
Here's the complete set of commands to run:

# Connect to stapp01
ssh tony@stapp01

# Install Docker CE
sudo yum update -y
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker

# Verify installation
docker --version
docker-compose --version
sudo systemctl status docker
This will complete the Docker installation on App Server 1 as requested by the Nautilus DevOps team.