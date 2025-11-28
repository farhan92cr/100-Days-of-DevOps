Step 1: Pre-requisite Verification
First, let's verify the app servers are accessible and prepare the directories:

bash
# From jump server, connect to each app server and create jenkins directories
ssh tony@stapp01 "mkdir -p /home/tony/jenkins && echo 'stapp01 ready'"
ssh steve@stapp02 "mkdir -p /home/steve/jenkins && echo 'stapp02 ready'"
ssh banner@stapp03 "mkdir -p /home/banner/jenkins && echo 'stapp03 ready'"
Step 2: Access Jenkins and Install Required Plugin
Access Jenkins UI:

URL: http://jenkins.stratos.xfusioncorp.com:8080 or http://172.16.238.19:8080

Username: admin

Password: Adm!n321

Install SSH Agent Plugin:

Go to Manage Jenkins → Manage Plugins

Click Available tab

Search for "SSH Build Agents plugin"

Check the plugin and click Install without restart

After installation, Restart Jenkins when prompted

Step 3: Configure SSH Credentials
For each app server, create SSH credentials:

Manage Jenkins → Manage Credentials

Global → Add Credentials

Credentials for stapp01:

Kind: SSH Username with private key

Scope: Global

ID: stapp01-tony-key

Username: tony

Private Key: → Enter directly

Key: Paste Tony's private SSH key

Description: Tony SSH key for stapp01

Credentials for stapp02:

Kind: SSH Username with private key

Scope: Global

ID: stapp02-steve-key

Username: steve

Private Key: → Enter directly

Key: Paste Steve's private SSH key

Description: Steve SSH key for stapp02

Credentials for stapp03:

Kind: SSH Username with private key

Scope: Global

ID: stapp03-banner-key

Username: banner

Private Key: → Enter directly

Key: Paste Banner's private SSH key

Description: Banner SSH key for stapp03

Step 4: Add Slave Nodes
Add App_server_1 (stapp01)
Manage Jenkins → Manage Nodes and Clouds

New Node

Node name: App_server_1

Permanent Agent → Create

Configuration Details:

Name: App_server_1

Description: Application Server 1 - stapp01

Number of executors: 2

Remote root directory: /home/tony/jenkins

Labels: stapp01

Usage: Use this node as much as possible

Launch method: Launch agents via SSH

Host: stapp01.stratos.xfusioncorp.com or 172.16.238.10

Credentials: Select stapp01-tony-key

Host Key Verification Strategy: Non verifying Verification Strategy

Availability: Keep this agent online as much as possible

Click Save

Add App_server_2 (stapp02)
Repeat the process:

New Node

Node name: App_server_2

Permanent Agent → Create

Configuration:

Name: App_server_2

Description: Application Server 2 - stapp02

Number of executors: 2

Remote root directory: /home/steve/jenkins

Labels: stapp02

Usage: Use this node as much as possible

Launch method: Launch agents via SSH

Host: stapp02.stratos.xfusioncorp.com or 172.16.238.11

Credentials: Select stapp02-steve-key

Host Key Verification Strategy: Non verifying Verification Strategy

Availability: Keep this agent online as much as possible

Click Save

Add App_server_3 (stapp03)
Repeat the process:

New Node

Node name: App_server_3

Permanent Agent → Create

Configuration:

Name: App_server_3

Description: Application Server 3 - stapp03

Number of executors: 2

Remote root directory: /home/banner/jenkins

Labels: stapp03

Usage: Use this node as much as possible

Launch method: Launch agents via SSH

Host: stapp03.stratos.xfusioncorp.com or 172.16.238.12

Credentials: Select stapp03-banner-key

Host Key Verification Strategy: Non verifying Verification Strategy

Availability: Keep this agent online as much as possible

Click Save

Step 5: Verify Node Status
Go to Manage Jenkins → Manage Nodes and Clouds

Check all three nodes:

App_server_1 - Should show green icon (Online)

App_server_2 - Should show green icon (Online)

App_server_3 - Should show green icon (Online)

If any node shows offline, click on the node and check Log for errors

Step 6: Test Slave Nodes
Create test jobs to verify each slave:

Test Job for stapp01:

New Item → Freestyle project → Name: test-stapp01

Restrict where this project can be run: stapp01

Build → Execute shell:

bash
echo "Running on: $(hostname)"
echo "Current user: $(whoami)"
echo "Working directory: $(pwd)"
ls -la
Build Now and check console output

Repeat for stapp02 and stapp03 with appropriate labels.

Step 7: Troubleshooting
If nodes won't connect:
Test SSH connectivity manually:

bash
# From Jenkins server, test each app server
ssh jenkins@jenkins.stratos.xfusioncorp.com

# Test each connection
ssh tony@stapp01 "hostname && whoami"
ssh steve@stapp02 "hostname && whoami" 
ssh banner@stapp03 "hostname && whoami"
Common issues and solutions:
SSH Connection refused:

Check if app servers are running

Verify SSH service is active: systemctl status sshd

Authentication failed:

Verify SSH keys are correct

Check username and permissions

Directory not found:

Ensure jenkins directories were created on app servers

Host not found:

Use IP addresses instead of hostnames in node configuration

Verification Checklist
All three nodes: App_server_1, App_server_2, App_server_3 created

Correct labels: stapp01, stapp02, stapp03 assigned

Correct remote directories: /home/tony/jenkins, /home/steve/jenkins, /home/banner/jenkins

All nodes show Online status (green icon)

Test jobs can execute on each slave node

No errors in node logs

Expected Result
After successful configuration, you should see all three slave nodes online and ready to execute jobs. You can now distribute builds across these app servers using their labels in your Jenkins pipelines.




-------------------------------------------------------------
--------------------------------------------
------------------------------------------

Generate New SSH Key Pairs
Since you likely don't have the existing private keys, generate new ones:

bash
# Generate SSH key pair for Tony on stapp01
ssh tony@stapp01 "ssh-keygen -t rsa -b 2048 -f /home/tony/.ssh/id_rsa -N ''"

# View the private key
ssh tony@stapp01 "cat /home/tony/.ssh/id_rsa"

# Copy the private key content

Copy the entire private key content (including -----BEGIN RSA PRIVATE KEY----- and -----END RSA PRIVATE KEY-----)


--------------------------------------------
----------------------------------------------
--------------------------------------------------


Solution: Use Password Authentication Instead
Since SSH keys are causing issues, let's use the password authentication method which is more reliable for this lab.

Step 1: Create New Credentials with Passwords
Delete the existing SSH key credentials and create new ones with passwords:

For stapp01 (Tony):
Manage Jenkins → Manage Credentials

Add Credentials:

Kind: Username with password

Scope: Global

Username: tony

Password: Ir0nM@n

ID: stapp01-tony-pwd

Description: Tony password for stapp01

For stapp02 (Steve):
Kind: Username with password

Scope: Global

Username: steve

Password: Am3ric@

ID: stapp02-steve-pwd

Description: Steve password for stapp02

For stapp03 (Banner):
Kind: Username with password

Scope: Global

Username: banner

Password: BigGr33n

ID: stapp03-banner-pwd

Description: Banner password for stapp03

Step 2: Update Node Configurations
Update App_server_1 (stapp01):
Go to Manage Jenkins → Manage Nodes and Clouds

Click on App_server_1 → Configure

Under Launch method → Credentials: Select stapp01-tony-pwd

Save

Update App_server_2 (stapp02):
Click on App_server_2 → Configure

Under Launch method → Credentials: Select stapp02-steve-pwd

Save

Update App_server_3 (stapp03):
Click on App_server_3 → Configure

Under Launch method → Credentials: Select stapp03-banner-pwd

Save





 Install Java on Each App Server
Run these commands from the jump server:

bash
# Install Java on stapp01 (Tony)
ssh tony@stapp01 "sudo yum install -y java-11-openjdk"

# Install Java on stapp02 (Steve)
ssh steve@stapp02 "sudo yum install -y java-11-openjdk"

# Install Java on stapp03 (Banner)
ssh banner@stapp03 "sudo yum install -y java-11-openjdk"
Step 2: Verify Java Installation
bash
# Verify Java on each server
ssh tony@stapp01 "java -version && which java"
ssh steve@stapp02 "java -version && which java"
ssh banner@stapp03 "java -version && which java"

For this specific lab java 17 need to install:


Install Java 17 on Each App Server
bash
# Install Java 17 on stapp01
ssh tony@stapp01 "sudo yum install -y java-17-openjdk"

# Install Java 17 on stapp02
ssh steve@stapp02 "sudo yum install -y java-17-openjdk"

# Install Java 17 on stapp03
ssh banner@stapp03 "sudo yum install -y java-17-openjdk"
Step 2: Verify Java 17 Installation
bash
# Check Java version on each server
ssh tony@stapp01 "java -version"
ssh steve@stapp02 "java -version"
ssh banner@stapp03 "java -version"







