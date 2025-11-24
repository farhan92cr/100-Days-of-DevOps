Jenkins Conditional Pipeline - Complete Solution
Step 1: Pre-requisite Setup
First, let's check the current state and fix permissions:

bash
# Check storage server access
ssh natasha@ststor01 "ls -la /var/www/html/"

# Check if it's already a git repository
ssh natasha@ststor01 "cd /var/www/html && git status 2>/dev/null || echo 'Not a git repo'"

# Fix permissions if possible
ssh natasha@ststor01 "sudo chown -R natasha:natasha /var/www/html 2>/dev/null || echo 'No sudo access'"
Step 2: Add Storage Server as Slave Node
Manage Jenkins → Manage Nodes and Clouds

New Node

Node name: Storage Server

Permanent Agent → Create

Configuration:

Name: Storage Server

Number of executors: 2

Remote root directory: /var/www/html

Labels: ststor01

Usage: Use this node as much as possible

Launch method: Launch agents via SSH

Host: ststor01.stratos.xfusioncorp.com or 172.16.238.15

Credentials: Create credentials for natasha (username: natasha, password: Bl@kW)

Host Key Verification Strategy: Non verifying Verification Strategy

Step 3: Create Jenkins Pipeline Job
New Item → Pipeline

Name: xfusion-webapp-job

Click OK

Step 4: Configure Pipeline with String Parameter
A. General Configuration
Check This project is parameterized

Add Parameter → String Parameter

Name: BRANCH

Description: Enter 'master' or 'feature' branch to deploy

Default Value: master

B. Pipeline Script
Use this conditional pipeline script:

groovy
pipeline {
    agent {
        label 'ststor01'
    }
    
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to deploy (master or feature)')
    }
    
    stages {
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying branch: ${params.BRANCH}"
                    
                    if (params.BRANCH == 'master') {
                        sh '''
                            echo "Deploying MASTER branch..."
                            cd /var/www/html
                            
                            # Initialize git if not already done
                            if [ ! -d .git ]; then
                                git init
                                git remote add origin http://gitea.stratos.xfusioncorp.com:3000/sarah/web_app.git
                            fi
                            
                            # Fetch and checkout master branch
                            git fetch origin
                            git checkout master -f
                            git reset --hard origin/master
                            
                            echo "Master branch deployed successfully!"
                            ls -la
                        '''
                    } else if (params.BRANCH == 'feature') {
                        sh '''
                            echo "Deploying FEATURE branch..."
                            cd /var/www/html
                            
                            # Initialize git if not already done
                            if [ ! -d .git ]; then
                                git init
                                git remote add origin http://gitea.stratos.xfusioncorp.com:3000/sarah/web_app.git
                            fi
                            
                            # Fetch and checkout feature branch
                            git fetch origin
                            git checkout feature -f
                            git reset --hard origin/feature
                            
                            echo "Feature branch deployed successfully!"
                            ls -la
                        '''
                    } else {
                        error "Invalid branch parameter. Use 'master' or 'feature'"
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo "Deployment process completed for branch: ${params.BRANCH}"
        }
    }
}
Step 5: Alternative Pipeline (Simpler Version)
If you have permission issues, use this alternative:

groovy
pipeline {
    agent {
        label 'ststor01'
    }
    
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to deploy')
    }
    
    stages {
        stage('Deploy') {
            steps {
                script {
                    echo "Selected branch: ${params.BRANCH}"
                    
                    sh """
                        cd /var/www/html
                        
                        # Ensure git is initialized
                        if [ ! -d .git ]; then
                            git init
                            git remote add origin http://gitea.stratos.xfusioncorp.com:3000/sarah/web_app.git
                        fi
                        
                        # Fetch all branches
                        git fetch --all
                        
                        # Checkout the selected branch
                        git checkout ${params.BRANCH} -f
                        git reset --hard origin/${params.BRANCH}
                        
                        echo "Successfully deployed ${params.BRANCH} branch"
                    """
                }
            }
        }
    }
}
Step 6: Manual Setup (If Pipeline Fails)
If the pipeline fails due to permission issues, run these commands first:

bash
# Setup git repository manually on storage server
ssh natasha@ststor01 "cd /var/www/html && git init && git remote add origin http://gitea.stratos.xfusioncorp.com:3000/sarah/web_app.git"

# Test both branches exist
ssh natasha@ststor01 "cd /var/www/html && git fetch origin && git branch -a"
Step 7: Test the Pipeline
Save the pipeline configuration

Build with Parameters

Test both scenarios:

BRANCH: master → should deploy master branch

BRANCH: feature → should deploy feature branch

Step 8: Verify Deployment
Click App button to test the website

Verify content loads at main URL (no subdirectory)

Test that different branches show different content (if they exist)

Step 9: Troubleshooting
If slave node won't connect:
bash
# Check permissions on /var/www/html
ssh natasha@ststor01 "ls -la /var/www/html/"

# If permission denied, try creating in home directory as temporary fix
ssh natasha@ststor01 "mkdir -p /home/natasha/jenkins"
If branch doesn't exist:
bash
# Check available branches
ssh natasha@ststor01 "cd /var/www/html && git fetch origin && git branch -r"
Step 10: Final Verification Checklist
Slave node: "Storage Server" with label ststor01 and remote directory /var/www/html

Pipeline job: xfusion-webapp-job created as Pipeline

String parameter: BRANCH parameter added

Single stage: Named exactly Deploy (case sensitive)

Conditional deployment:

master parameter → deploys master branch

feature parameter → deploys feature branch

Website access: Main URL loads content directly (no /web_app subdirectory)

This pipeline will conditionally deploy either the master or feature branch based on the parameter value, meeting all the requirements specified.

