Jenkins User Permissions Setup - Complete Guide
Step 1: Install Required Plugin
First, we need to install the Role-based Authorization Strategy plugin:

Access Jenkins: http://jenkins.stratos.xfusioncorp.com:8080

Username: admin

Password: Adm!n321

Install Plugin:

Go to Manage Jenkins → Manage Plugins

Click Available tab

Search for "Role-based Authorization Strategy"

Check the plugin and click Install without restart

Restart Jenkins when prompted

Step 2: Configure Security Realm (Verify Users)
Manage Jenkins → Configure Global Security

Under Security Realm:

Ensure Jenkins' own user database is selected

Check Allow users to sign up (temporarily if users don't exist)

Save

Step 3: Create Users (If They Don't Exist)
If the users sam and rohan don't exist yet:

Manage Jenkins → Manage Users

Create User (repeat for both):

Username: sam

Password: sam@pass12345

Full name: sam

Email: sam@xfusioncorp.com

Create User:

Username: rohan

Password: rohan@pass12345

Full name: rohan

Email: rohan@xfusioncorp.com

Step 4: Enable Role-Based Strategy
Manage Jenkins → Configure Global Security

Under Authorization:

Select Role-Based Strategy

Click Apply

Save

Step 5: Configure Role-Based Permissions
Manage Jenkins → Manage and Assign Roles

A. Manage Roles
Global roles:

Ensure sam and rohan have Overall Read permission

They should NOT have Admin or other global permissions

Item roles:

Click Add Role

Role name: packages_access

Pattern: Packages (exact job name)

Permissions to add:

Build: Build

Cancel: Cancel

Configure: Configure

Read: Read

Update: Update

Tag: Create and Delete (for tagging)

Click Add

B. Assign Roles
Go to Assign Roles tab

Global roles:

Find sam and rohan in user list

Assign Overall Read role to both

Item roles:

For sam: Assign packages_access role

For rohan: Assign packages_access role

Step 6: Configure Job-Specific Permissions (Alternative Method)
If the above method doesn't work, use the Project-based Matrix Authorization Strategy:

Manage Jenkins → Configure Global Security

Under Authorization:

Select Project-based Matrix Authorization Strategy

Click Apply

Add users and permissions:

In the user/group field, type sam and click Add

In the user/group field, type rohan and click Add

Set permissions for sam:

Check for user sam:

✅ Job → Build

✅ Job → Configure

✅ Job → Read

Set permissions for rohan:

Check for user rohan:

✅ Job → Build

✅ Job → Cancel

✅ Job → Configure

✅ Job → Read

✅ Job → Update

✅ Job → Create (for tagging)

✅ Job → Delete (for tagging)

Ensure inheritance is set:

Make sure Inherit permissions from parent ACL is selected

Click Apply and Save

Step 7: Configure Job-Level Permissions
Go to Packages job page

Click Configure

Scroll down to Enable project-based security (if available)

Or look for Authorization section in job configuration

Ensure the permissions are inherited from global settings

Step 8: Verify Permissions
Test sam's permissions:
Should be able to:

View the Packages job

Build the job

Configure the job

Should NOT be able to:

Cancel builds (unless they started them)

Update job parameters beyond their configuration

Create/delete tags

Test rohan's permissions:
Should be able to:

View the Packages job

Build the job

Cancel builds

Configure the job

Update job parameters

Create and delete tags

Step 9: Manual Verification
Test each user's access:

Log out as admin

Log in as sam (sam/sam@pass12345)

Verify:

Can see Packages job

Can click Build Now

Can click Configure

Log out and log in as rohan (rohan/rohan@pass12345)

Verify:

Can see Packages job

Can click Build Now

Can click Configure

Can cancel builds

Can update job parameters

Can create/delete tags

Step 10: Troubleshooting
If users can't see the job:
Check if they have Overall Read permission

Verify user accounts exist and passwords are correct

If specific permissions don't work:
Use Project-based Matrix method instead of Role-based

Ensure Inherit permissions from parent ACL is selected

If plugin installation fails:
Restart Jenkins manually:

bash
ssh jenkins@jenkins.stratos.xfusioncorp.com "sudo systemctl restart jenkins"
Final Verification Checklist
sam can: Build, Configure, Read Packages job

rohan can: Build, Cancel, Configure, Read, Update, Tag Packages job

Inherit permissions from parent ACL is selected

No other job configurations were modified

Both users can successfully log in and access the Packages job

This setup ensures that both developers have the exact permissions required for the Packages job while maintaining the principle of least privilege.


--------------------------------
-----------------------------
-------------------------------

Step 3: Configure Job-Specific Permissions
Now go to the specific "Packages" job:

Click on "Packages" job from the main dashboard

Click Configure

Look for "Enable project-based security" or "Authorization" section in the job configuration

If you see project-based security options:

Enable it

Add sam and rohan users

Set the specific permissions for each user

If you DON'T see project-based security in the job:
This means the inheritance is automatic when using Project-based Matrix at global level.



