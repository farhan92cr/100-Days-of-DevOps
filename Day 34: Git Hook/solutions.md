cat > post-update << 'EOF'
#!/usr/bin/sh

# Arguments to post-update
DATE=$(date +%F)        # YYYY-MM-DD
GIT_DIR=$(pwd)

        echo "Creating tag release-$DATE"
        git --git-dir="$GIT_DIR" tag "release-$DATE" master
EOF