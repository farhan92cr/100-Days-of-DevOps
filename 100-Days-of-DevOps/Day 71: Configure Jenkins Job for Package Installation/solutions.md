Lab Purpose & Explanation
What This Lab Does:
This creates an automated package installer using Jenkins. Instead of manually logging into servers to install software, you can now use Jenkins to install any package on remote servers with a single click.

Benefits:
Time Saving: No manual SSH required

Consistency: Same installation process every time

Access Control: Control who can install packages

Audit Trail: All installations are logged

Key Terms:
Parameterized Job: A job that accepts user input (like package names)

SSH Plugin: Allows Jenkins to connect to remote servers

Build Step: The actual command that runs on the remote server

Complete Step-by-Step Guide:
STEP 1: Login to Jenkins
Click the Jenkins button on top bar

Login with:

Username: admin

Password: Adm!n321

You should see the Jenkins dashboard

STEP 2: Install Required Plugin
Click Manage Jenkins (on left sidebar)

Click Manage Plugins

Click Available plugins tab

In the search box, type: ssh

Find Publish Over SSH plugin

Check the checkbox next to it

Click Install without restart

Wait for installation to complete

If prompted to restart, check "Restart Jenkins when installation is complete" and wait

STEP 3: Configure Passwordless Sudo on Storage Server
From Jump Host Terminal:

bash
# Connect to storage server
ssh natasha@ststor01
# Password: Bl@kW

# Now switch to root to configure sudo
sudo su -
# When prompted for password, use: Bl@kW
On ststor01 as root:

bash
# Configure passwordless sudo for natasha
echo "natasha ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Verify the configuration
cat /etc/sudoers | grep natasha

# Exit root
exit

# Test if it works - should NOT ask for password
sudo whoami
# Should return: root
STEP 4: Configure SSH Connection in Jenkins
In Jenkins, click Manage Jenkins → Configure System

Scroll down to Publish over SSH section

Under SSH Servers, click Add button

Fill in these exact details:

Name: storage-server

Hostname: ststor01

Username: natasha

Password: Bl@kW

Click Test Configuration button

You should see "Success" message

Click Save at the bottom

STEP 5: Create the Job
Back to Jenkins main page

Click New Item (on left sidebar)

Enter Item name: install-packages

Select Freestyle project

Click OK

STEP 6: Configure Parameters
In the job configuration page, check This project is parameterized

Click Add Parameter → String Parameter

Fill in:

Name: PACKAGE

Description: Enter package name to install

Default Value: (leave empty)

Leave other fields as default

STEP 7: Configure Build Step
Scroll down to Build section

Click Add build step dropdown

Select Execute shell script on remote host using ssh

In the configuration that appears:

SSH Site: Select storage-server (the one we created)

Command: Copy and paste exactly:

bash
sudo yum install -y $PACKAGE
STEP 8: Save the Job
Scroll to bottom

Click Save

STEP 9: Test the Job
On the job page, click Build with Parameters

In the PACKAGE field, type: wget (small, reliable package)

Click Build

You'll be redirected to the build page

Click the build number (#1) in Build History

Click Console Output to see the installation progress

Expected Success Output:
In Console Output, you should see:

text
Loaded plugins: fastestmirror, ovl
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package wget.x86_64 0:1.14-18.el7_6.1 will be installed
--> Finished Dependency Resolution
Complete!
Finished: SUCCESS
Troubleshooting Common Issues:
If SSH Connection Fails:
Verify ststor01 is reachable from Jenkins server

Check username/password: natasha / Bl@kW

Test manually from jump host: ssh natasha@ststor01

If Sudo Still Asks for Password:
On ststor01, verify sudo configuration:

bash
sudo cat /etc/sudoers | grep natasha
It should show: natasha ALL=(ALL) NOPASSWD: ALL

If Package Installation Fails:
Try different packages: vim, curl, tree

Check if ststor01 has internet access

Verify the command in Jenkins: sudo yum install -y $PACKAGE

If Plugin Not Found:
Make sure you're searching in Available plugins tab

Search for: Publish Over SSH

If not found, try updating plugin site in Advanced tab

Test Packages to Try:
wget - download tool

vim - text editor

tree - directory listing

curl - web transfer tool

htop - system monitor

Final Verification:
Run job with at least 2 different packages

Check Console Output for "Complete!" message

Verify job shows "Finished: SUCCESS"

Your automated package installer is now ready! 🎉



