Step 1: Connect to Storage Server (ststor01)
bash
ssh natasha@ststor01
# Password: Bl@kW
Step 2: Navigate to the Git Repository
bash
cd /usr/src/kodekloudrepos/cluster
Step 3: Check Commit History on Feature Branch
bash
sudo git checkout feature
sudo git log --oneline --grep="Update info.txt"
Step 4: Identify the Commit Hash
bash
# Find the exact commit hash with message "Update info.txt"
sudo git log --oneline feature | grep "Update info.txt"
# Note the commit hash (e.g., abc1234)
Step 5: Switch to Master Branch
bash
sudo git checkout master
Step 6: Cherry-pick the Specific Commit
bash
# Replace abc1234 with the actual commit hash from step 4
sudo git cherry-pick <commit-hash>
Step 7: Verify the Cherry-pick
bash
sudo git log --oneline -n 3
Step 8: Push Changes to Remote
bash
sudo git push origin master
✅ Verification Commands:
bash
# Verify commit was cherry-picked
sudo git log --oneline -n 3

# Check if info.txt was updated
sudo git show HEAD --name-only

# Verify both branches exist
sudo git branch -a
🔍 What We Accomplished:
Found the commit: Located the "Update info.txt" commit on feature branch

Cherry-picked: Applied that specific commit to master branch

Pushed changes: Updated remote repository

Selective merge: Only merged one commit, not the entire feature branch

💡 Why Cherry-pick?
Allows merging specific commits without merging the whole branch

Useful when you want only certain changes from a feature branch

Maintains clean commit history on master

The specific commit "Update info.txt" has been successfully merged to master branch! 🚀