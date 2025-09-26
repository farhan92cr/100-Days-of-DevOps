Day 32 - Step-by-Step Solution:

Step 1: Connect to Storage Server (ststor01)
ssh natasha@ststor01
# Password: Bl@kW


Step 2: Navigate to the Git Repository
cd /usr/src/kodekloudrepos/news


Step 3: Check Current Branches and Status
git branch -a
git status


Step 4: Fetch Latest Changes from Remote
git fetch origin


Step 5: Switch to Feature Branch
git checkout feature


Step 6: Rebase Feature Branch with Master
git rebase origin/master


Step 7: Resolve Conflicts (if any)
# If conflicts occur:
git status                    # See conflicted files
# Edit files to resolve conflicts
git add .                    # Mark conflicts as resolved
git rebase --continue        # Continue rebase


Step 8: Push Rebased Feature Branch
git push origin feature --force-with-lease
✅ Verification Commands:
# Verify rebase was successful
git log --oneline -n 5 --graph

# Check that feature branch is ahead of master
git log --oneline origin/master..feature

# Verify no merge commits
git log --oneline --merges -n 3
🔍 What We Accomplished:
Rebased feature branch: Applied feature commits on top of latest master

No merge commit: Clean, linear history

Preserved all data: All feature branch changes maintained

Updated remote: Pushed the rebased branch

💡 Why --force-with-lease?
Safer than --force - won't overwrite others' work

Only pushes if remote hasn't changed since you last fetched

🎯 Result:
text
Before:                   After:
A---B---C  master        A---B---C  master
     \                          \
      D---E  feature             D'---E'  feature (rebased)
The feature branch has been successfully rebased with master without any merge commits! 🚀



