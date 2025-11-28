Step 1: Connect to Storage Server (ststor01)
ssh natasha@ststor01
# Password: Bl@kW


Step 2: Navigate to the Git Repository
cd /usr/src/kodekloudrepos/blog


Step 3: Add New Remote dev_blog
git remote add dev_blog /opt/xfusioncorp_blog.git


Step 4: Verify New Remote Added
git remote -v


Step 5: Copy the index.html File
cp /tmp/index.html .


Step 6: Add and Commit the File to Master Branch
git checkout master
git add index.html
git commit -m "Added index.html file"


Step 7: Push Master Branch to New Remote
git push dev_blog master


Step 8: Verify Everything
# Check remotes
git remote -v

# Check file exists
ls -la index.html

# Check commit history
git log --oneline -n 3
✅ What We Accomplished:
Added remote: dev_blog pointing to /opt/xfusioncorp_blog.git

Copied file: /tmp/index.html to repository

Committed changes: To master branch

Pushed: Master branch to the new remote dev_blog

🔍 Verification Commands:
bash
# Verify remote was added
git remote -v
# Should show: dev_blog /opt/xfusioncorp_blog.git

# Verify file was committed
git log --oneline -n 2
# Should show the "Added index.html file" commit

# Verify push was successful
git status
# Should show: "Your branch is ahead of 'origin/master' by 1 commit" (but this is normal since we pushed to dev_blog, not origin)
The changes are now complete with the new remote added, file committed, and pushed to the new remote repository! 🚀

