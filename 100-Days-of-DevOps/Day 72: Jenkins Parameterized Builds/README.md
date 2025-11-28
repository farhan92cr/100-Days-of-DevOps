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