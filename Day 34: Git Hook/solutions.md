cat > post-update << 'EOF'
#!/usr/bin/sh

# Arguments to post-update
DATE=$(date +%F)        # YYYY-MM-DD
GIT_DIR=$(pwd)

        echo "Creating tag release-$DATE"
        git --git-dir="$GIT_DIR" tag "release-$DATE" master
EOF


Step 1: Connect to Storage Server
bash
ssh natasha@ststor01
Step 2: ONLY Create Hook in Bare Repository
bash
cd /opt/news.git/hooks
cat > post-update << 'EOF'
#!/bin/bash
DATE=$(date +%Y-%m-%d)
git tag "release-$DATE" master
EOF
chmod +x post-update
Step 3: ONLY Test Hook (as required)
bash
cd /opt/news.git
hooks/post-update
Step 4: ONLY Merge and Push from Working Copy
bash
cd /usr/src/kodekloudrepos/news
git checkout master
git merge feature
git push origin master
Step 5: ONLY Basic Verification
bash
git fetch --tags
git tag -l "release-*"


Step 1: Connect to Storage Server
bash
ssh natasha@ststor01
Step 2: Create Hook in BARE Repository (EXACT community solution)
bash
cd /opt/apps.git/hooks
cat > post-update << 'EOF'
#!/usr/bin/sh

# Arguments to post-update
DATE=$(date +%F)        # YYYY-MM-DD
GIT_DIR=$(pwd)

        echo "Creating tag release-$DATE"
        git --git-dir="$GIT_DIR" tag "release-$DATE" master
EOF
chmod +x post-update
Step 3: Test the Hook (as required)
bash
cd /opt/apps.git
hooks/post-update
Step 4: Merge and Push from Working Copy (EXACT community workflow)
bash
cd /usr/src/kodekloudrepos/apps
git checkout master
git merge feature
git push origin master
Step 5: Verify
bash
git fetch --tags
git tag -l "release-*"
🎯 Key Points from Community Solution:
Hook in bare repo ONLY (/opt/apps.git/hooks)

Use /usr/bin/sh not /bin/bash

Simple tag command - git tag "release-$DATE" master

NO force tagging (-f) - this was their mistake

Basic workflow - merge → push (hook auto-creates tag)

✅ This is the EXACT solution that worked for the community member!
Follow this precisely and it should work! 🚀




