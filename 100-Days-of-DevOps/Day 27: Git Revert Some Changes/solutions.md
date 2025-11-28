Step 1: Connect to Storage Server (ststor01)
bash
ssh natasha@ststor01
# Password: Bl@kW
Step 2: Navigate to the Git Repository
bash
cd /usr/src/kodekloudrepos/blog
Step 3: Check Commit History
bash
git log --oneline -n 5
Step 4: Revert the Latest Commit with Correct Message
bash
git revert HEAD --no-edit
Step 5: Change Commit Message to "revert blog"
bash
git commit --amend -m "revert blog"
Step 6: Verify the Revert
bash
git log --oneline -n 3
Step 7: Check Status
bash
git status
✅ Verification Commands:
bash
# Check the latest commits
git log --oneline -n 3
# Should show:
# <hash> revert blog
# <hash> [the commit that was reverted] 
# <hash> initial commit

# Verify working directory is clean
git status
🔍 What We Did:
Reverted HEAD: Using git revert HEAD

Set message: Changed commit message to "revert blog" (all lowercase)

Maintained history: Safe operation that doesn't rewrite git history

The repository HEAD has been successfully reverted with the correct commit message! 🚀
