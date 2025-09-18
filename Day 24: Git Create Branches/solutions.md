
Step 1: Connect to Storage Server (ststor01)

ssh natasha@ststor01
# Password: Bl@kW
Step 2: Navigate to the Git Repository



cd /usr/src/kodekloudrepos/beta
Step 3: Check Current Status and Ensure on Master



git status
git checkout master
git pull origin master  # Ensure up-to-date
Step 4: Create New Branch xfusioncorp_beta



git checkout -b xfusioncorp_beta
Step 5: Verify New Branch Creation

[natasha@ststor01 beta]$ sudo git checkout -b xfusioncorp_beta
Switched to a new branch 'xfusioncorp_beta'
[natasha@ststor01 beta]$ sudo git branch
  kodekloud_beta
  master
* xfusioncorp_beta

# Check current branch
git branch

# Verify it was created from master
git log --oneline -n 3

[natasha@ststor01 beta]$ sudo git log --oneline -n 3
59c374e (HEAD -> xfusioncorp_beta, origin/master, master) initial commit
[natasha@ststor01 beta]$

Step 6: Optional - Push to Remote
git push origin xfusioncorp_beta
Verification Commands:

# Verify branch exists
git branch

# Confirm no code changes were made
git status

# Show last few commits to verify branch origin
git log --oneline -n 3 --graph
✅ What We Did:
Created: New branch xfusioncorp_beta from master

Location: /usr/src/kodekloudrepos/beta

No code modifications: As required

Ready for development: Team can start new features


# Should show: * xfusioncorp_beta
git branch

# Should show clean working directory
git status

# Should show same commits as master
git log --oneline -n 3
The new branch xfusioncorp_beta is now created and ready for the development team to implement new features without affecting the main codebase! 🚀




