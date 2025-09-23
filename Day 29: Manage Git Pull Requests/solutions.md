Day 29 - Step-by-Step Solution:

Part 1: SSH to Storage Server and Verify Repository

Step 1: Connect as max user
ssh max@ststor01
# Password: Max_pass123

Step 2: Navigate to the Repository
cd ~/story-blog  # or whatever the repo name is

Step 3: Check Repository Contents
ls -la
cat story.txt  # or whatever the story file is called

Step 4: Verify Git History
git log --oneline
git log --pretty=format:"%h - %an: %s" -n 5

Step 5: Check Branches
git branch -a
git checkout story/fox-and-grapes
git log --oneline -n 3


Part 2: Create Pull Request (PR) via Gitea UI

Step 6: Access Gitea UI
Click "Gitea UI" button on top bar

Login: max / Max_pass123

Step 7: Create Pull Request
Navigate to the repository

Click "Pull Requests" tab

Click "New Pull Request"

Source: story/fox-and-grapes

Destination: master

Title: Added fox-and-grapes story

Click "Create Pull Request"

Step 8: Add Reviewer
In the new PR, find "Reviewers" section (right sidebar)

Click "Add Reviewer"

Search and select "tom"

Confirm adding tom as reviewer

Part 3: Review and Approve as Tom
Step 9: Logout and Login as Tom
Logout of Gitea UI

Login: tom / Tom_pass123

Step 10: Review and Merge PR
Navigate to Pull Requests

Find PR: "Added fox-and-grapes story"

Click on the PR to open it

Review the changes (files tab)

Click "Review" or "Merge" button

Select "Merge" option

Confirm the merge

📸 Required Screenshots:
max's git log showing commits and author

PR creation page with source/destination branches

Reviewers section with tom added

PR merged confirmation as tom

✅ Verification:
bash
# After merge, verify as max user
git checkout master
git pull origin master
git log --oneline -n 3
ls -la  # Should see the new story file
🎯 What We Accomplished:
Verified max's story in the feature branch

Created PR for code review process

Added reviewer (tom) for approval

Merged the story to master after review

Followed proper Git workflow with code review

The story has been successfully merged through proper code review process! 📖✨







EXTRA:
I can see the issue. When GitHub says "These branches are equal" and shows no differences, it means your story/fox-and-grapes branch hasn't been pushed to the remote repository yet, or it's not detecting the differences properly.

Here's how to fix this:

Step 1: Push Your Branch to Remote
bash
git push origin story/fox-and-grapes
Step 2: Refresh the GitHub Page
After pushing, refresh the GitHub page in your browser.

Step 3: Create the Pull Request
Now you should see differences between the branches, and you'll be able to:

Create the Pull Request - The button should now be active

Add Reviewer - After creating the PR, look for the "Reviewers" section in the right sidebar

Click "Add Reviewer" and search for "tom"

If You Still Don't See "Add Reviewer" Option:
The "Reviewers" section might be:

Collapsed - Look for a "Reviewers" heading and click to expand

In a different location - Sometimes it's under "Assignees" or in the main PR description area

Missing permissions - You might need write access to the repository to add reviewers

