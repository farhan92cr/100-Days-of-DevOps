The Nautilus DevOps team has recently setup a Jenkins server, which they want to use for some CI/CD jobs. Before that they want to install some plugins which will be used in most of the jobs. Please find below more details about the task



1. Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and Adm!n321 password.


2. Once logged in, install the Git and GitLab plugins. Note that you may need to restart Jenkins service to complete the plugins installation, If required, opt to Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre.

Note:

1. After restarting the Jenkins service, wait for the Jenkins login page to reappear before proceeding.

2. For tasks involving web UI changes, capture screenshots to share for review or consider using screen recording software like loom.com for documentation and sharing.



SOLUTION:

///////////////need to install plugins before/////////////////////
////////////////////////////////////////////////////////////
The "Project-based Matrix Authorization Strategy" option is not available in your current Jenkins setup. This is likely because the necessary plugin isn't installed.

Here's how to get that option:
1. Install the Required Plugin
Go to Manage Jenkins → Plugins

Click on Available plugins tab

Search for: "Matrix Authorization Strategy" or "Role-based Authorization Strategy"

Check the box and click Install

Restart Jenkins after installation

2. Alternative Immediate Solutions
Since you only have those three basic options, here are your current choices:

Option A: "Logged-in users can do anything" (Recommended temporary solution)

More secure than "Anyone can do anything"

At least requires users to log in first

You can then control access through user accounts

Option B: Use "Legacy mode"

Preserves the security model from older Jenkins versions

Might give you more granular control than the basic options

3. What to Do Now
Select "Logged-in users can do anything" as a temporary measure

Then install the Matrix Authorization plugin as described above

After restart, you'll see the "Project-based Matrix Authorization Strategy" option/////////////////////////////////////////////////
//////////////////////////////////////////////////////////////
////////////////////


Step 1: Access Jenkins UI
Open Jenkins URL: http://172.16.238.19:8080

Login with:

Username: theadmin

Password: Adm!n321

Step 2: Install Plugins
Method 1: Through Jenkins UI
Navigate to Plugin Manager:

Click "Manage Jenkins" on the left sidebar

Click "Manage Plugins"

Install Git Plugin:

Go to the "Available" tab

Search for "Git" in the filter

Check the box for "Git" plugin

Click "Install without restart"

Install GitLab Plugin:

In the same "Available" tab

Search for "GitLab"

Check the box for "GitLab" plugin

Click "Install without restart"

Wait for Installation:

The system will download and install both plugins

Once completed, you'll see the option to "Restart Jenkins when no jobs are running"

Check this option to restart Jenkins

Method 2: Alternative Approach (if UI method fails)
If you have issues with the UI, you can install via CLI:

bash
# On Jenkins server via SSH
# Install plugins using Jenkins CLI
sudo java -jar /var/cache/jenkins/war/WEB-INF/jenkins-cli.jar -s http://localhost:8080/ -auth theadmin:Adm!n321 install-plugin git gitlab -restart
Step 3: Wait for Restart
After triggering the restart:

Jenkins will shut down and restart automatically

Wait for the login page to reappear (this may take 1-2 minutes)

Monitor the restart process:

bash
# Check Jenkins status
sudo systemctl status jenkins

# Check Jenkins logs
sudo tail -f /var/log/jenkins/jenkins.log
Step 4: Verify Installation
After Jenkins restarts and you can log back in:

Verify plugins are installed:

Go to Manage Jenkins → Manage Plugins → Installed tab

Search for "Git" and "GitLab" - both should show as installed

Check plugin functionality:

Create a new job (New Item)

In the configuration, you should see Git/GitLab options in Source Code Management section

Troubleshooting
If plugins don't install properly:

bash
# Restart Jenkins manually if needed
sudo systemctl restart jenkins

# Check plugin installation directory
sudo ls -la /var/lib/jenkins/plugins/ | grep -E "git|gitlab"

# Force plugin installation via CLI
sudo java -jar /var/cache/jenkins/war/WEB-INF/jenkins-cli.jar -s http://localhost:8080/ -auth theadmin:Adm!n321 install-plugin git -deploy
sudo java -jar /var/cache/jenkins/war/WEB-INF/jenkins-cli.jar -s http://localhost:8080/ -auth theadmin:Adm!n321 install-plugin gitlab -deploy
sudo systemctl restart jenkins
Important Notes:
Wait patiently during plugin installation and restart

Don't interrupt the process once started

Take screenshots of the plugin installation process for documentation

After restart, verify both plugins are properly installed

The installation should complete successfully, and you'll have both Git and GitLab plugins available for your CI/CD jobs.
