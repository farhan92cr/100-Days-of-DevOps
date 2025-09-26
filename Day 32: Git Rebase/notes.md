🔄 Git Rebase:
Rewriting commit history to create a linear timeline, moving or combining commits for cleaner project history.

Rebase = "Clean up and reorganize my commit history"



🔄 Git Rebase - The "Clean History" Tool

📊 What is Git Rebase?
bash
# Before rebase:
A---B---C  feature-branch
     \
      D---E  master

# After rebase:
A---B---C---D---E  feature-branch
🎯 Why Use Rebase?
Clean history: Linear commit timeline

No merge commits: Cleaner git log

Easier code review: Sequential changes

🔧 Common Rebase Commands:
bash
git checkout feature-branch
git rebase master           # Replay feature commits on top of master
git rebase -i HEAD~3       # Interactive rebase (edit/squash commits)
⚠️ Rebase vs Merge:
Rebase: Rewrites history (use before pushing)

Merge: Preserves history (safe for shared branches)

🚀 Simple Analogy:
Stash = Putting documents in a drawer temporarily

Rebase = Rearranging pages in a document for better flow

Stash is for temporary storage, Rebase is for cleaning up history! 🎯

