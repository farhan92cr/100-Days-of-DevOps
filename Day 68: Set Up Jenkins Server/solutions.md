Lab Purpose:
This lab focuses on setting up Jenkins - a popular Continuous Integration/Continuous Deployment (CI/CD) tool. Jenkins automates the process of building, testing, and deploying applications, making software development more efficient and reliable.

New Terms Explained:
Jenkins:
An open-source automation server used for CI/CD

Automates building, testing, and deploying software

Provides plugins for integrating with various tools and technologies

CI/CD (Continuous Integration/Continuous Deployment):
CI: Developers frequently merge code changes into a central repository where automated builds and tests run

CD: Automated deployment of code changes to testing/staging/production environments

Yum:
Yellowdog Updater Modified

Package manager for RPM-based Linux distributions (like CentOS/RHEL)

Used to install, update, and remove software packages

Server Information:
Server: jenkins (172.16.238.19)

User: root

Password: S3curePass

Step-by-Step Solution:

1  Set Up Jenkins Server:
The DevOps team at xFusionCorp Industries is initiating the setup of CI/CD pipelines and has decided to utilize Jenkins as their server. Execute the task according to the provided requirements:



1. Install Jenkins on the jenkins server using the yum utility only, and start its service.

If you face a timeout issue while starting the Jenkins service, refer to this.
2. Jenkin's admin user name should be theadmin, password should be Adm!n321, full name should be Mark and email should be mark@jenkins.stratos.xfusioncorp.com.


Note:

1. To access the jenkins server, connect from the jump host using the root user with the password S3curePass.

2. After Jenkins server installation, click the Jenkins button on the top bar to access the Jenkins UI and follow on-screen instructions to create an admin user.



SOLUTION:


Step 1: Connect to Jenkins Server
First, connect from the jump host to the Jenkins server:

bash
# From jump host, connect to Jenkins server
ssh jenkins@172.16.238.19
# Password: j@rv!s


Install wget first

# Install wget using yum
sudo yum install -y wget

# Now run the Jenkins repo command
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

Step 3:Install Java (required dependency)
bash
# Install Java if not already installed
sudo yum install -y java-11-openjdk-devel

# Verify Java installation
java -version
javac -version


Step 4: If installation still fails, try this alternative approach
bash
# Download Jenkins directly and install
sudo wget https://pkg.jenkins.io/redhat-stable/jenkins-2.414.3-1.1.noarch.rpm
sudo rpm -ivh jenkins-2.414.3-1.1.noarch.rpm


Step 5Start Jenkins service
bash
# Start Jenkins service
sudo systemctl start jenkins

# Enable to start on boot
sudo systemctl enable jenkins

# Check status
sudo systemctl status jenkins


Step : Access Jenkins and Create Admin User
Access Jenkins UI:

Open a web browser and go to: http://172.16.238.19:8080

Get initial admin password:

bash
# On Jenkins server, get the initial admin password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Follow the setup wizard:

Use the initial admin password to unlock Jenkins

Select "Install suggested plugins"

When prompted to create first admin user, enter:

Username: theadmin

Password: Adm!n321

Full name: Mark

Email: mark@jenkins.stratos.xfusioncorp.com

==============================================
Important step::Cration via script, as there no option add email in GLI in the above step
so need add via CLI
=======================================
# Stop Jenkins
sudo systemctl stop jenkins

# Create the user directory structure manually
sudo mkdir -p /var/lib/jenkins/users/theadmin

# Create the user config file
sudo cat > /var/lib/jenkins/users/theadmin/config.xml << 'EOF'
<?xml version='1.1' encoding='UTF-8'?>
<user>
  <version>1.0</version>
  <fullName>Mark</fullName>
  <properties>
    <hudson.tasks.Mailer_-UserProperty>
      <emailAddress>mark@jenkins.stratos.xfusioncorp.com</emailAddress>
    </hudson.tasks.Mailer_-UserProperty>
    <jenkins.security.LastGrantedAuthoritiesProperty>
      <roles>
        <string>authenticated</string>
      </roles>
      <timestamp>1690000000000</timestamp>
    </jenkins.security.LastGrantedAuthoritiesProperty>
  </properties>
</user>
EOF

# Fix permissions
sudo chown -R jenkins:jenkins /var/lib/jenkins/users/

# Start Jenkins
sudo systemctl start jenkins

