Step 1: Connect to Storage Server (ststor01)
ssh natasha@ststor01
# Password: Bl@kW


Step 2: Navigate to the Git Repository
cd /usr/src/kodekloudrepos/demo


Step 3: Check Available Stashes
git stash list
# Should show stash@{0}, stash@{1}, etc.


Step 4: Apply the Specific Stash (stash@{1})
git stash apply stash@{1}


Step 5: Verify the Changes Were Restored
git status
# Should show the restored files as modified/untracked
git diff  # To see what changes were restored


Step 6: Add and Commit the Restored Changes
git add .
git commit -m "Restored stashed changes from stash@{1}"


Step 7: Push Changes to Origin
git push origin master

✅ Verification Commands:
# Verify stash was applied
git status

# Verify commit was created
git log --oneline -n 2

# Verify push was successful
git status
🔍 What We Accomplished:
Found stashed changes: Using git stash list

Restored specific stash: stash@{1} using git stash apply

Committed changes: Made the stashed changes permanent

Pushed to remote: Shared the changes with the team

💡 Key Points:
git stash apply applies the stash but keeps it in the stash list

git stash pop would apply and remove it from stash list

We used stash@{1} specifically as requested

The stashed changes from stash@{1} have been successfully restored, committed, and pushed to the demo repository! 🚀