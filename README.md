# 100 days of DevOps Journey - Day-wise Practice
#########################################################

1  echo "# 100 days of DevOps Journey - Day-wise Practice" > README.md
    2  echo "*.log" > .gitignore
    3  echo "temp/" >> .gitignore
    4  # Create main directory structure
    5  mkdir -p Day-{01..100}
    6  for day in Day-{01..100}; do     mkdir -p $day/{tasks,solutions};     touch $day/README.md $day/notes.md; done
    7  cd Day-001