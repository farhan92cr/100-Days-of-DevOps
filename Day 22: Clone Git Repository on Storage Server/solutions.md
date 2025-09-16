Step 1: Connect to Storage Server (ststor01)
ssh natasha@ststor01
# Password: Bl@kW


Step 2: Create Target Directory (if doesn't exist)
sudo mkdir -p /usr/src/kodekloudrepos


Step 3: Clone the Repository
cd /usr/src/kodekloudrepos
git clone /opt/cluster.git


Step 4: Verify the Clone
# Check if repository was cloned
ls -la /usr/src/kodekloudrepos/cluster

# Verify it contains git files
ls -la /usr/src/kodekloudrepos/cluster/.git


Step 5: Check Repository Status (Optional)
cd /usr/src/kodekloudrepos/cluster
git status
git log --oneline -n 3



Verification Commands: (Optional)
# Verify the clone was successful
ls -ld /usr/src/kodekloudrepos/cluster

# Check it's a valid git repository
git --git-dir=/usr/src/kodekloudrepos/cluster/.git status

# Verify no permissions were changed
ls -la /usr/src/kodekloudrepos/ | grep cluster
✅ What We Did:
Cloned: /opt/cluster.git → /usr/src/kodekloudrepos/cluster

User: Used natasha user (no sudo needed for cloning)

No modifications: Didn't change any permissions or alter existing directories

Result: Full working copy of the repository ready for development

The repository is now successfully cloned and ready for the development team to start using! 🚀