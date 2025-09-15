Step 1: Connect to Storage Server (ststor01)
ssh natasha@ststor01
# Password: Bl@kW


Step 2: Install Git Package
sudo yum install git -y


Step 3: Create Bare Git Repository
sudo git init --bare /opt/official.git



Step 4: Set Proper Permissions (just for refrence no need for excercise)
sudo chown -R natasha:natasha /opt/official.git
sudo chmod -R 755 /opt/official.git



Step 5: Verify the Repository
# Check if repository was created
ls -la /opt/official.git

# Verify it's a bare repository
git --git-dir=/opt/official.git config --get core.bare
# Should return: true
Step 6: Test the Repository (Optional)
bash
# Create a test clone to verify it works
cd /tmp
git clone /opt/official.git test_repo
cd test_repo
git config user.email "test@example.com"
git config user.name "Test User"
echo "test file" > test.txt
git add test.txt
git commit -m "Initial commit"
git push origin master
Verification Commands:
# Verify git is installed
git --version

# Verify repository exists and is bare
ls -la /opt/official.git
head -n 2 /opt/official.git/HEAD

# Check repository type
git --git-dir=/opt/official.git rev-parse --is-bare-repository
# Should return: true
✅ What We Created:
Bare repository: No working directory, just the .git contents

Location: /opt/official.git (exact name as required)

Purpose: Central repository for developers to push/pull code

Permissions: Properly set for the natasha user

The bare repository is now ready for developers to use as their central code repository! 🚀