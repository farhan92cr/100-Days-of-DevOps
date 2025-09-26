🌿 Git Stash:
Temporary storage for uncommitted changes when you need to switch tasks but aren't ready to commit.
OR:
Stash = "Save my current work temporarily"


🌿 Git Stash - The "Save for Later" Feature

🧳 What is Git Stash?
bash
# When you have uncommitted changes but need to switch branches
git stash          # Save changes temporarily
git switch other-branch  # Work on something else
git switch back    # Return to original branch  
git stash pop      # Restore your changes
💡 When to Use Stash:
Emergency fix: Need to switch branches quickly

Not ready to commit: Changes are incomplete

Save work: Before pulling/pulling new changes

🔧 Common Stash Commands:
bash
git stash                    # Save changes
git stash list               # View all stashes
git stash apply stash@{1}    # Restore specific stash
git stash pop               # Restore and remove from stash
git stash drop stash@{1}    # Delete a stash