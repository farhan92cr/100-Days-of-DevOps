Lab Purpose & Explanation
What This Lab Does:
This lab teaches you how to create parameterized builds in Jenkins. Parameterized builds allow you to pass different inputs to your Jenkins jobs, making them more flexible and reusable.

Benefits:
Flexibility: One job can handle multiple scenarios

Reusability: Same job with different parameters

User Input: Users can customize job behavior

Automation: Different environments with same pipeline

Key Terms Explained:
Parameterized Build:
A Jenkins job that accepts user input before running

Like a function that takes parameters

Makes jobs dynamic and configurable

String Parameter:
Simple text input field

User can type any value

Example: Stage names, version numbers

Choice Parameter:
Dropdown list of predefined options

User selects from available choices

Example: Environment selection (Dev/Staging/Prod)

Shell Command:
Linux commands executed during the job

Can use parameter values in commands

Example: echo "Deploying to $env"

Step-by-Step Guide:
STEP 1: Login to Jenkins
Click the Jenkins button on top bar

Login with:

Username: admin

Password: Adm!n321

STEP 2: Create New Job
Click New Item (on left sidebar)

Enter Item name: parameterized-job

Select Freestyle project

Click OK

STEP 3: Add String Parameter
Check This project is parameterized

Click Add Parameter → String Parameter

Configure the string parameter:

Name: Stage

Description: Build stage parameter

Default Value: Build

Leave other fields as default

STEP 4: Add Choice Parameter
Click Add Parameter again

Select Choice Parameter

Configure the choice parameter:

Name: env

Description: Select deployment environment

Choices: Enter each on a new line:

text
Development
Staging
Production
Leave other fields as default

STEP 5: Configure Build Step
Scroll down to Build section

Click Add build step → Execute shell

In the Command box, enter:

bash
echo "Stage parameter value: $Stage"
echo "Environment parameter value: $env"
echo "Job executed successfully!"
STEP 6: Save the Job
Scroll to bottom

Click Save

STEP 7: Test the Job
On the job page, click Build with Parameters

You should see two parameters:

Stage: (pre-filled with "Build")

env: Dropdown with Development, Staging, Production

Select Production from the env dropdown

Click Build

STEP 8: Verify Execution
Click the build number (#1) in Build History

Click Console Output

You should see:

text
Stage parameter value: Build
Environment parameter value: Production
Job executed successfully!
Finished: SUCCESS
Expected Results:
Parameter Screen:
Two parameters visible: Stage (text) and env (dropdown)

Stage defaults to "Build"

env dropdown shows: Development, Staging, Production

Console Output (when env=Production):
text
Started by user admin
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/parameterized-job
[parameterized-job] $ /bin/sh -xe /tmp/jenkins123456789.sh
+ echo 'Stage parameter value: Build'
Stage parameter value: Build
+ echo 'Environment parameter value: Production'
Environment parameter value: Production
+ echo 'Job executed successfully!'
Job executed successfully!
Finished: SUCCESS
Real-World Use Cases:
Deployment Scenarios:
Development: Deploy to test servers

Staging: Deploy to pre-production

Production: Deploy to live servers

Build Scenarios:
Build: Compile code only

Test: Run tests only

Deploy: Build and deploy

Troubleshooting:
If Parameters Don't Appear:
Verify you checked This project is parameterized

Check parameter names are correct: Stage and env

Make sure you saved the configuration

If Build Fails:
Check the shell command syntax

Verify parameter names in echo commands match exactly

Check Console Output for error messages

If Choice Parameter Shows Wrong Options:
Edit job configuration

Verify choices are on separate lines

Check for typos in environment names

Advanced Tips:
Using Parameters in Complex Commands:
bash
echo "Deploying to $env environment"
echo "Current stage: $Stage"
Conditional Logic with Parameters:
bash
if [ "$env" = "Production" ]; then
    echo "This is PRODUCTION deployment - be careful!"
else
    echo "This is $env environment"
fi
Final Verification:
✅ Job created: parameterized-job
✅ Two parameters: Stage (string) and env (choice)
✅ Default values working
✅ Build successful with env=Production
✅ Console output shows both parameter values

Your parameterized Jenkins job is now ready! 🎉


