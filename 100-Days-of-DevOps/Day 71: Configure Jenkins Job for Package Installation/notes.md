Issue:
The SSH connection is failing because Jenkins is trying to use "Bl@kW" as a hostname instead of a password. This suggests the SSH plugin configuration has an issue.

Complete Step-by-Step Fix:
STEP 1: Reconfigure SSH Connection Properly
In Jenkins, go to Manage Jenkins → Configure System

Scroll down to Publish over SSH section

Remove the existing storage-server configuration:

Find the Delete button next to it and click it

Add a new SSH Server:

Click Add button

Fill in EXACTLY as below:

text
Name: storage-server
Hostname: ststor01.stratos.xfusioncorp.com
Username: natasha
For Password field:

Click Advanced button

In Password field, enter: Bl@kW

Make sure Use password authentication is checked

Test the connection:

Click Test Configuration button

You should see: "Success"

Click Save

STEP 2: Alternative - Use IP Address
If hostname still doesn't work, use the IP address:

Edit the SSH configuration again:

Hostname: 172.16.238.15 (ststor01 IP)

Username: natasha

Password: Bl@kW

STEP 3: Verify SSH Connection Manually
From Jump Host, test the connection:

bash
ssh natasha@ststor01.stratos.xfusioncorp.com
# Password: Bl@kW

# Once connected, test sudo
sudo whoami
# Should return: root (without password prompt)
STEP 4: Update Jenkins Job Command
Go to install-packages job → Configure

In the Build section, verify the command is:

bash
sudo yum install -y $PACKAGE
SSH Site should be: storage-server

Click Save

STEP 5: Test the Job
Click Build with Parameters

Enter: wget

Click Build

Check Console Output

Alternative Approach: Direct SSH Command
If the SSH plugin continues to fail, use this approach:

STEP A: Install SSH Agent Plugin
Manage Jenkins → Manage Plugins

Install SSH Agent Plugin

Restart if needed

STEP B: Configure Job with SSH Agent
Edit install-packages job

In Build Environment, check SSH Agent

Select appropriate credentials

In Build section, use Execute shell with:

bash
ssh -o StrictHostKeyChecking=no natasha@ststor01.stratos.xfusioncorp.com "sudo yum install -y $PACKAGE"
Quick Diagnostic:
Check if Jenkins can reach ststor01:

bash
# From Jenkins server terminal
ping ststor01.stratos.xfusioncorp.com
If ping fails, use IP address in SSH configuration.

Final Working Configuration:
SSH Server Settings:
Name: storage-server

Hostname: ststor01.stratos.xfusioncorp.com (or 172.16.238.15)

Username: natasha

Password: Bl@kW

Test: Should show "Success"

Job Command:
bash
sudo yum install -y $PACKAGE
The issue is in the SSH plugin configuration - make sure the password is in the correct field and the hostname is properly formatted!


