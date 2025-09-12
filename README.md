# 100 days of DevOps Journey - Day-wise Practice
#########################################################

1  echo "# 100 days of DevOps Journey - Day-wise Practice" > README.md
    2  echo "*.log" > .gitignore
    3  echo "temp/" >> .gitignore
    4  # Create main directory structure
    5  mkdir -p Day-{01..100}
    6  for day in Day-{01..100}; do     mkdir -p $day/{tasks,solutions};     touch $day/README.md $day/notes.md; done
    7  cd Day-001

    Pushing Code from GitHub Codespace to GitHub RepoIn a GitHub Codespace:

1.  git add .   --> to stage all changes or git add <file> for specific files.
3.  git commit -m "Your commit message"  -->to commit changes.
4.  git push -->to push changes to the linked GitHub repository.