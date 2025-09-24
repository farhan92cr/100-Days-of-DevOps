Day 30 - Step-by-Step Solution:

Step 1: Connect to Storage Server (ststor01)
ssh natasha@ststor01
# Password: Bl@kW


Step 2: Navigate to the Git Repository
cd /usr/src/kodekloudrepos/ecommerce


Step 3: Check Current Commit History
git log --oneline


Step 4: Find the Commit Hash for "add data.txt file"
git log --oneline --grep="add data.txt file"
# Note the commit hash (e.g., abc1234)


Step 5: Perform Hard Reset to That Commit
git reset --hard <commit-hash>
# Replace <commit-hash> with the actual commit hash from step 4


Step 6: Force Push to Update Remote
git push --force origin master


Step 7: Verify the Reset
git log --oneline
# Should show only 2 commits:
# 1. add data.txt file
# 2. initial commit


✅ Verification Commands:
# Verify only 2 commits exist
git log --oneline
# Output should be:
# <hash> add data.txt file
# <hash> initial commit

# Check working directory is clean
git status

# Verify remote is updated
git log --oneline origin/master -n 2
🔍 What We Accomplished:
Reset history: Removed all commits after "add data.txt file"

Only 2 commits left: "initial commit" and "add data.txt file"

Force pushed: Updated remote repository to match local reset

Clean state: Repository now has only the desired commits

⚠️ Important Note:
git reset --hard is destructive - it permanently removes commits

git push --force overwrites remote history

Only use this when you're sure you want to erase commit history

💡 Alternative Safe Approach (if reset fails):
bash
# Create new branch from the desired commit
git checkout -b clean_master <commit-hash>
git push --force origin clean_master:master
The repository history has been successfully cleaned to contain only the two required commits! 🚀

