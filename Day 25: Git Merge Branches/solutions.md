Step 1: Connect to Storage Server (ststor01)
ssh natasha@ststor01
# Password: Bl@kW


Step 2: Navigate to the Git Repository
cd /usr/src/kodekloudrepos/media


Step 3: Ensure You're on Master and Up-to-Date
git checkout master
git pull origin master



Step 4: Create New Branch xfusion
git checkout -b xfusion



Step 5: Copy the index.html File
cp /tmp/index.html .


Step 6: Add and Commit the File
git add index.html
git commit -m "Added index.html file"


Step 7: Switch Back to Master and Merge
git checkout master
git merge xfusion


Step 8: Push Both Branches to Origin
git push origin master
git push origin xfusion


Step 9: Verify Everything
# Check branches
git branch -a

# Check file exists in both branches
git checkout master && ls -la index.html
git checkout xfusion && ls -la index.html

# Check commit history
git log --oneline -n 5 --graph --all
✅ What We Accomplished:
Created branch: xfusion from master

Copied file: /tmp/index.html to repository

Committed changes: In the new branch

Merged back: xfusion branch into master

Pushed: Both branches to remote origin

🔍 Verification Commands:
bash
# Verify file exists
ls -la index.html

# Verify merge commit
git log --oneline -n 3 --graph

# Verify remote branches
git branch -r
The changes are now complete with the new file added, merged, and pushed to both branches! 🚀