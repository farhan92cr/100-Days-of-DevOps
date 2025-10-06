
Here’s a few things to make sure that you’re doing the right thing (just cos your request doesn’t contain a lot of detail, let’s ensure you have the following down):

Make sure that your hook is setup on your bare repo (i.e. /opt/official.git or whatever the question throws at you)
Here’s a snippet of the git hook that worked for me:
#My question was on /opt/media.git folder so-- /opt/media.git/hooks/post-update

#!/usr/bin/sh

# Arguments to post-update
DATE=$(date +%F)        # YYYY-MM-DD
GIT_DIR=$(pwd)

        echo "Creating tag release-$DATE"
        git --git-dir="$GIT_DIR" tag "release-$DATE" master

and rememember to make the file executable chmod +x /opt/media.git/hooks/post-update

In this post 11, I outline what had me stuck for a few days. I was tagging the release but also forcing the tag unto subsequent commits (cos I was worried that the system would attempt to commit and get a failure). I now believe that I was marked wrong cos I did a git tag -f…

change dir into your working directory (in my case it was: cd /usr/src/kodekloudrepos/media
You should be on the “feature branch”, so switch to “master”

git checkout master
git merge feature
git push